---
title: 'Tutorial: Conecte-se a uma aplicação web usando um ponto final Azure Private'
titleSuffix: Azure Private Link
description: Começa com este tutorial usando o Azure Private endpoint para ligar a um webapp em privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896978"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Tutorial: Ligar-se a uma aplicação Web com um Ponto Final Privado do Azure

O ponto final privado Azure é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem com os recursos de Private Link em privado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual e um hospedeiro de bastião.
> * Cria uma máquina virtual.
> * Crie um webapp.
> * Criar um ponto final privado.
> * Teste conectividade para web app private endpoint.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!Note]
> O Private Endpoint está disponível em regiões públicas para aplicações web PremiumV2 de nível PremiumV3, aplicações web Do 43 ºC, aplicações web Linux e o plano Azure Functions Premium (por vezes referido como o plano Elastic Premium). 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e hospedeiro de bastião

Nesta secção, você vai criar uma rede virtual, sub-rede e hospedeiro de bastião. 

O hospedeiro de bastião será utilizado para ligar de forma segura à máquina virtual para testar o ponto final privado.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **myResourceGroup** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **Europa Ocidental** |

3. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

4. No separador **endereços IP,** introduza estas informações:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Espaço de endereço IPv4 | Insira **10.1.0.0/16** |

5. No **nome da sub-rede,** selecione a palavra **predefinição**.

6. Na **sub-rede Editar,** insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome da sub-rede | Insira **mySubnet** |
    | Intervalo de endereços da sub-rede | Insira **10.1.0.0/24** |

7. Selecione **Guardar**.

8. Selecione o separador **Segurança.**

9. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |


8. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

9. Selecione **Criar**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Nesta secção, irá criar uma máquina virtual que será usada para testar o ponto final privado.


1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual compute de recurso ou procurar por máquina  >    >   **Virtual** na caixa de pesquisa.
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM** |
    | Region | Selecione **Europa Ocidental** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **Windows Server 2019 Datacenter - Gen1** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | **myVNet** |
    | Sub-rede | **mySubnet** |
    | IP público | Selecione **Nenhuma**. |
    | Grupo de segurança de rede NIC | **Básica**|
    | Portas de entrada públicas | Selecione **Nenhuma**. |
   
5. Selecione **Rever + criar**. 
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="create-web-app"></a>Criar aplicação Web

Nesta secção, irá criar uma aplicação web.

1. No menu à esquerda, selecione **Criar uma**  >  Aplicação Web de armazenamento **de**  >  recursos, ou procure a **Web App** na caixa de pesquisa.

2. No **separador Básicos** da **Create Web App** insira ou selecione as seguintes informações:

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Name | Insira **mywebapp**. Se o nome não estiver disponível, insira um nome único. |
    | Publicar | Selecione **Código**. |
    | Pilha de runtime | **Selecione .NET Core 3.1 (LTS)**. |
    | Sistema Operativo | Selecione **Windows**. |
    | Region | Selecione **Europa Ocidental** |
    | **Plano do Serviço de Aplicações** |  |
    | Plano Windows (Europa Ocidental) | Selecione **Criar novo**. </br> Insira **o meu Plano de Serviço** em **Nome**. |
    | SKU e tamanho | Selecione **Alterar tamanho**. </br> Selecione **P2V2** no ecrã **Spec Picker.** </br> Selecione **Aplicar**. |
   
3. Selecione **Rever + criar**.

4. Selecione **Criar**.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Separador básico de criar aplicativo web no portal Azure." border="true":::

## <a name="create-private-endpoint"></a>Criar ponto final privado

1. No menu à esquerda, selecione **All Resources**  >  **mywebapp** ou o nome que escolheu durante a criação.

2. Na visão geral da aplicação web, selecione **Settings**  >  **Networking**.

3. Em **Networking**, selecione **Configurar as suas ligações de ponto final privado**.

4. **Selecione + Adicione** no ecrã de **ligações Private Endpoint.**

5. Introduza ou selecione as seguintes informações no ecrã **Add Private Endpoint:**

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o meu ponto de aplicação daweb**. |
    | Subscrição | Selecione a sua subscrição. |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | Integrar com zona DNS privada | Selecione **Yes** (Sim). |

6. Selecione **OK**.
    

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar à aplicação web através do ponto final privado.

1. Selecione **grupos** de recursos no painel de navegação à esquerda.

2. Selecione **myResourceGroup**.

3. Selecione **myVM**.

4. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de ligar.

8. Introduza `nslookup <webapp-name>.azurewebsites.net`. **\<webapp-name>** Substitua-o pelo nome da aplicação web que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Um endereço IP privado de **10.1.0.5** é devolvido para o nome da aplicação web.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.

9. Abra um navegador web no seu computador local e introduza o URL externo da sua aplicação web, **https:// \<webapp-name> .azurewebsites.net**.

10. Verifique se recebe uma página **403.** Esta página indica que a aplicação web não é acessível externamente.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="403 páginas para endereço de aplicativo web externo." border="true":::

11. Na ligação de bastião ao **myVM,** abra o Internet Explorer.

12. Introduza o url da sua aplicação web, **https:// \<webapp-name> .azurewebsites.net**.

13. Verifique se recebe a página de aplicações web predefinido.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Página de aplicativo web padrão." border="true":::

18. Feche a ligação ao **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e a aplicação web com os seguintes passos:

1. A partir do menu da esquerda, selecione **Grupos de Recursos.**

2. Selecione **myResourceGroup**.

3. Selecione **Eliminar grupo de recursos**.

4. Introduza **o myResourceGroup** em **TIPO O NOME DO GRUPO DE RECURSOS**.

5. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Saiba como criar um serviço de Ligação Privada:
> [!div class="nextstepaction"]
> [Criar um serviço de Ligação Privada](create-private-link-service-portal.md)
