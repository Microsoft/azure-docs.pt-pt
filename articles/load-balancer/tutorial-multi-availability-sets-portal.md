---
title: 'Tutorial: Criar um equilibrador de carga com mais de uma disponibilidade definida na piscina de backend - Portal Azure'
titleSuffix: Azure Load Balancer
description: Neste tutorial, coloque um Balançador de Carga Azure com mais de uma disponibilidade definida na piscina de backend.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602357"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Tutorial: Criar um equilibrador de carga com mais de uma disponibilidade definida na piscina de backend utilizando o portal Azure

Como parte de uma alta disponibilidade, as máquinas virtuais são frequentemente agrupadas em vários conjuntos de disponibilidade. 

O Balancer de Carga suporta mais de um conjunto de disponibilidades com máquinas virtuais na piscina de backend.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma rede virtual
> * Criar uma porta de entrada NAT para a conectividade de saída
> * Criar um balanceador de carga SKU Azure padrão
> * Crie quatro máquinas virtuais e dois conjuntos de disponibilidade
> * Adicione máquinas virtuais em conjuntos de disponibilidade para recossar o pool de equilibrador de carga
> * Testar o balanceador de carga

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Nesta secção, irá criar uma rede virtual para o equilibrador de carga e os outros recursos utilizados no tutorial.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Na caixa de pesquisa no topo do portal, insira a **rede Virtual.**

3. Nos resultados da pesquisa, selecione **redes Virtuais.**

4. Selecione **+ Criar**.

5. No **separador Básico da** **rede virtual Create,** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ------|
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Criar novo**. </br> **Insira tutorLBmultiAVS-rg** em **nome**. |
    | **Detalhes da instância** |   |
    | Name | Introduza **myVNet**. |
    | Region | Selecione **(E.U.A.) E.U.A. Oeste 2**. |

6. Selecione o separador **endereços IP** ou o botão **Seguinte: Endereços IP** na parte inferior da página.

7. No separador **endereços IP,** no nome **da sub-rede** selecione **predefinido**.

8. No painel **de sub-redes Editar,** em **nome da sub-rede** insira **o myBackendSubnet**.

9. Selecione **Guardar**.

10. Selecione o separador **Segurança** ou o **seguinte: Botão de segurança** na parte inferior da página.

11. No separador **Segurança,** em **BastionHost** selecione **Enable**.

12. Insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome de bastião | Insira **MyBastionHost**. |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/27**. |
    | Endereço IP público | Selecione **Criar novo**. </br> Insira **o myBastionIP** em **Nome**. |

13. Selecione o **separador 'Rever +' ou** o botão **'Análise azul' + criar** botão na parte inferior da página.

14. Selecione **Criar**.

## <a name="create-nat-gateway"></a>Criar um NAT gateway 

Nesta secção, você vai criar uma porta de entrada NAT para a conectividade de saída das máquinas virtuais.

1. Na caixa de pesquisa no topo do portal, insira o **gateway NAT**.

2. Selecione as portas NAT nos **resultados** da pesquisa.

3. Selecione **+ Criar**.

4. No separador Básico do gateway de tradução de endereços de **rede (NAT),** introduza ou selecione as **seguintes** informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | **Selecione TutorlbmultiAVS-rg**. |
    | **Detalhes da instância** |   |
    | Nome do gateway NAT | Insira **o myNATgateway**. |
    | Region | Selecione **(E.U.A.) E.U.A. Oeste 2**. |
    | Zona de disponibilidade | Selecione **Nenhuma**. |
    | Tempo de 20 minutos (minutos) | Insira **15**. |

5. Selecione o **separador IP outbound** ou selecione o botão **IP de saída seguinte** na parte inferior da página.

6. **Selecione Criar um novo endereço IP público** ao lado de **endereços IP públicos** no **separador IP de saída.**

7. **Insira o myPublicIP-nat** em **nome**.

8. Selecione **OK**.

9. Selecione o separador **Sub-rede** ou selecione o botão **Seguinte: Sub-rede** na parte inferior da página.

10. Selecione **myVNet** no menu pull-down na **rede Virtual**.

11. Selecione a caixa de verificação ao lado **do myBackendSubnet**.

12. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

13. Selecione **Criar**.

## <a name="create-load-balancer"></a>Criar um balanceador de carga

Nesta secção, irá criar um equilibrador de carga para as máquinas virtuais.

1. Na caixa de pesquisa no topo do portal, introduza o **balançador de carga**.

2. Selecione **os equilibradores de carga** nos resultados da pesquisa.

3. Selecione **+ Criar**.

4. No separador Básico do **Equilibrador** de Carga, insira ou selecione as **seguintes** informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | **Selecione TutorlbmultiAVS-rg**. |
    | **Detalhes da instância** |   |
    | Name | Insira **o myLoadBalancer**. |
    | Region | Selecione **(E.U.A.) E.U.A. Oeste 2**. |
    | Tipo | Deixe o padrão do **Público.** |
    | SKU | Deixe o padrão da **Norma**. |
    | Escalão de serviço | Deixe o padrão da **Regional**. |
    | **Endereço IP público** |   |
    | Endereço IP público | Deixe o padrão de **Criar novo**. |
    | Nome do endereço IP público | Insira **o myPublicIP-lb**. |
    | Zona de disponibilidade | Selecione **Zona redundante**. |
    | Adicionar um endereço IPv6 público | Deixe o padrão de **Nº**. |
    | Preferência de encaminhamento | Deixe o padrão da **rede Microsoft**. |

5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

6. Selecione **Criar**.

### <a name="configure-load-balancer-settings"></a>Configurar definições de balançadores de carga

Nesta secção, você vai criar uma piscina de backend para **myLoadBalancer**.

Irá criar uma sonda de saúde para monitorizar **HTTP** e **Porto 80**. A sonda de saúde monitorizará a saúde das máquinas virtuais na piscina de backend. 

Criará uma regra de equilíbrio de carga para **o Porto 80** com SNAT de saída desativado. A porta de entrada NAT que criou anteriormente irá lidar com a conectividade de saída das máquinas virtuais.

1. Na caixa de pesquisa no topo do portal, introduza o **balançador de carga**.

2. Selecione **os equilibradores de carga** nos resultados da pesquisa.

3. Selecione **myLoadBalancer**.

4. No **myLoadBalancer**, selecione **backend pools** em **Definições**.

5. **Selecione + Adicione** em **piscinas backend**.

6. In **Add backend pool,** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myBackendPool**. |
    | Rede virtual | Selecione **myVNet**. |
    | Configuração da piscina de backend | Deixe o padrão de **NIC**. |
    | Versão IP | Deixe o padrão de **IPv4**. |

7. Selecione **Adicionar**.

8. Selecione **sondas de saúde**.

9. Selecione **+ Adicionar**.

10. In **Add health probe**, insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPProbe**. |
    | Protocolo | Selecione **HTTP**. |
    | Porta | Deixe o padrão de **80**. |
    | Caminho | Deixe o padrão de **/** . |
    | Intervalo | Deixe o padrão de **5** segundos. |
    | Limiar com funcionamento incorreto | Deixe o incumprimento de **2** falhas consecutivas. |

11. Selecione **Adicionar**.

12. Selecione **regras de equilíbrio de carga**. 

13. Selecione **+ Adicionar**.

14. Introduza ou selecione as seguintes informações na **regra de equilíbrio de carga adicionar:**

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **myHTTPRule**. |
    | Versão IP | Deixe o padrão de **IPv4**. |
    | Endereço IP frontend | Selecione **LoadBalancerFrontEnd**. |
    | Protocolo | Selecione o padrão de **TCP**. |
    | Porta | Insira **80**. |
    | Porta de back-end | Insira **80**. |
    | Conjunto de back-end | Selecione **myBackendPool**. |
    | Sonda de estado de funcionamento | Selecione **myHTTPProbe**. |
    | Persistência da sessão | Deixe o padrão de **Nenhum**. |
    | Tempo de 20 minutos (minutos) | Mude o deslizador para **15**. |
    | Reset TCP | Selecione **Ativado**. |
    | IP Flutuante | Deixe o padrão de **Desativado**. |
    | Tradução de endereços de rede de saída (SNAT) | Deixe o padrão de **(Recomendado) Utilize regras de saída para fornecer aos membros do pool backend acesso à internet.** |

5. Selecione **Adicionar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Nesta secção, irá criar dois grupos de disponibilidade com duas máquinas virtuais por grupo. Estas máquinas serão adicionadas ao pool de backend do equilibrador de carga durante a criação. 

### <a name="create-first-set-of-vms"></a>Criar o primeiro conjunto de VMs

1. **Selecione + Crie um recurso** na secção superior esquerda do portal.

2. Em **Nova,** selecione Máquina Virtual **Compute**  >  .

3. No separador Básicos de **Criar uma máquina virtual,** insira ou selecione as **seguintes** informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição |
    | Grupo de recursos | **Selecione TutorlbmultiAVS-rg**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira **o myVM1**. |
    | Region | Selecione **(E.U.A.) E.U.A. Oeste 2**. |
    | Opções de disponibilidade | Selecione **Conjunto de disponibilidade**. |
    | Conjunto de disponibilidade | Selecione **Criar novo**. </br> Insira **o mySilabilitySet1** no **nome**. </br> Selecione **OK**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server - Gen1**. |
    | Instância do Azure Spot | Deixe o padrão de não verificado. |
    | Tamanho | Selecione um tamanho para a máquina virtual. |
    | **Conta de administrador** |   |
    | Nome de utilizador | Introduza um nome de utilizador. |
    | Palavra-passe | Introduza uma senha. |
    | **Regras portuárias de entrada** |   |
    | Portas de entrada públicas | Selecione **Nenhuma**. |

4. Selecione o **separador 'Rede'** ou selecione o **seguinte: Discos** e, em **seguida, seguinte: Botão de rede** na parte inferior da página.

5. No **separador Networking,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **myBackendSubnet**. |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Avançadas**. |
    | Configure grupo de segurança de rede | Selecione **Criar novo**. </br> Em **Nome,** insira **myNSG**. </br> **Selecione +Adicione uma regra de entrada nas** **regras de entrada**. </br> Selecione **HTTP** para **o serviço.** </br> Insira **myHTTPrule** para **nome**. </br> Selecione **Adicionar**. </br> Selecione **OK**. | 
    | **Balanceamento de carga** |   |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione a caixa de verificação. |
    | **Definições de equilíbrio de carga** |   |
    | Opções de equilíbrio de carga | Selecione **o equilibrador de carga Azure**. |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**. |
    | Selecione uma piscina de backend | Selecione **myBackendPool**. |

6. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

7. Selecione **Criar**.

8. Repita os passos 1 a 7 para criar a segunda máquina virtual do conjunto. Substitua as definições do VM com as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myVM2**. |
    | Conjunto de disponibilidade | Selecione **mySilabilitySet1**. |
    | Rede Virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **myBackendSubnet**. |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede | Selecione **myNSG**. |
    | Opções de equilíbrio de carga | Selecione **o equilibrador de carga Azure**. |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**. |
    | Selecione uma piscina de backend | Selecione **myBackendPool**. |

### <a name="create-second-set-of-vms"></a>Criar segundo conjunto de VMs

1. **Selecione + Crie um recurso** na secção superior esquerda do portal.

2. Em **Nova,** selecione Máquina Virtual **Compute**  >  .

3. No separador Básicos de **Criar uma máquina virtual,** insira ou selecione as **seguintes** informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição |
    | Grupo de recursos | **Selecione TutorlbmultiAVS-rg**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira **o myVM3**. |
    | Region | Selecione **(E.U.A.) E.U.A. Oeste 2**. |
    | Opções de disponibilidade | Selecione **Conjunto de disponibilidade**. |
    | Conjunto de disponibilidade | Selecione **Criar novo**. </br> Insira **o mySilabilitySet2** no **nome**. </br> Selecione **OK**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server - Gen1**. |
    | Instância do Azure Spot | Deixe o padrão de não verificado. |
    | Tamanho | Selecione um tamanho para a máquina virtual. |
    | **Conta de administrador** |   |
    | Nome de utilizador | Introduza um nome de utilizador. |
    | Palavra-passe | Introduza uma senha. |
    | **Regras portuárias de entrada** |   |
    | Portas de entrada públicas | Selecione **Nenhuma**. |

4. Selecione o **separador 'Rede'** ou selecione o **seguinte: Discos** e, em **seguida, seguinte: Botão de rede** na parte inferior da página.

5. No **separador Networking,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **myBackendSubnet**. |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | Selecione **Avançadas**. |
    | Configure grupo de segurança de rede | Selecione **myNSG**. | 
    | **Balanceamento de carga** |   |
    | Colocar esta máquina virtual por trás de uma solução de equilíbrio de carga existente? | Selecione a caixa de verificação. |
    | **Definições de equilíbrio de carga** |   |
    | Opções de equilíbrio de carga | Selecione **o equilibrador de carga Azure**. |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**. |
    | Selecione uma piscina de backend | Selecione **myBackendPool**. |

6. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

7. Selecione **Criar**.

8. Repita os passos 1 a 7 para criar a segunda máquina virtual do conjunto. Substitua as definições do VM com as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o myVM4**. |
    | Conjunto de disponibilidade | Selecione **mySilabilitySet2**. |
    | Rede Virtual | Selecione **myVNet**. |
    | Grupo de segurança de rede | Selecione **myNSG**. |
    | Opções de equilíbrio de carga | Selecione **o equilibrador de carga Azure**. |
    | Selecione um equilibrador de carga | Selecione **myLoadBalancer**. |
    | Selecione uma piscina de backend | Selecione **myBackendPool**. |

## <a name="install-iis"></a>Instalar o IIS

Nesta secção, utilizará o anfitrião Azure Bastion que criou anteriormente para ligar às máquinas virtuais e instalar o IIS.

1. Na caixa de pesquisa no topo do portal, introduza a **máquina Virtual**.

2. Selecione **máquinas virtuais** nos resultados da pesquisa.

3. Selecione **myVM1**.

4. Na página **geral** do myVM1, selecione **Connect**  >  **Bastion**.

5. Selecione **Utilizar o Bastião**.

6. Introduza o **nome de utilizador** e **palavra-passe** que criou quando criou a máquina virtual.

7. Selecione **Ligar**.

7. No ambiente de trabalho do servidor, navegue para o **Windows Administrative Tools** Windows  >  **PowerShell**.

8. Na Janela PowerShell, executar os seguintes comandos para:

    * Instale o servidor IIS
    * Remova o ficheiro de iisstart.htm predefinido
    * Adicione um novo ficheiro iisstart.htm que exibe o nome do VM:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Feche a sessão de Bastião com **o myVM1**.

9. Repita os passos 1 a 8 para **o myVM2,** **myVM3** e **myVM4**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta secção, você vai descobrir o endereço IP público do equilibrador de carga. Utilizará o endereço IP para testar o funcionamento do equilibrador de carga.

1. Na caixa de pesquisa no topo do portal, insira **IP Público**.

2. Selecione **endereços IP públicos** nos resultados da pesquisa.

3. Selecione **myPublicIP-lb**.

4. Note o endereço IP público listado no **endereço IP** na página **geral** do **myPublicIP-lb**:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Encontre o endereço IP público do esquilibrador de carga." border="true":::

5. Abra um navegador web e insira o endereço IP público na barra de endereços:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Teste o balançador de carga com o navegador web." border="true":::

6. Selecione refresh no navegador para ver o tráfego equilibrado para as outras máquinas virtuais na piscina de backend.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o equilibrador de carga e os recursos de apoio com os seguintes passos:

1. Na caixa de pesquisa no topo do portal, insira o **grupo De Recursos**.

2. Selecione **grupos de recursos** nos resultados da pesquisa.

3. **Selecione TutorlbmultiAVS-rg**.

4. Na página geral do **TutorLBmultiAVS-rg,** selecione **Delete resource group**.

5. **Insira tutorLBmultiAVS-rg** em **TIPO O NOME DO GRUPO DE RECURSOS**.

6. Selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criou uma rede virtual e anfitrião do Azure Bastion.
* Criei um Equilibrador de Carga Padrão Azure.
* Criei dois conjuntos de disponibilidade com duas máquinas virtuais por conjunto.
* Instalou o IIS e testou o equilibrador de carga.

Avance para o próximo artigo para aprender a criar um balanceador de carga azure cross-region:
> [!div class="nextstepaction"]
> [Criar um equilibrador de carga transversal](tutorial-cross-region-portal.md)

