---
title: Quickstart - Criar um ponto final privado utilizando o portal Azure
description: Utilize este quickstart para aprender a criar um Ponto Final Privado utilizando o portal Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018074"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Quickstart: Criar um ponto final privado utilizando o portal Azure

Começa com o Azure Private Link utilizando um Private Endpoint para ligar de forma segura a uma aplicação web Azure.

Neste arranque rápido, você vai criar um ponto final privado para uma aplicação web Azure e implementar uma máquina virtual para testar a ligação privada.  

Os pontos finais privados podem ser criados para diferentes tipos de serviços Azure, tais como Azure SQL e Azure Storage.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Uma App Web Azure com um plano de serviço de aplicações **premiumV2** ou superior implementado na sua subscrição Azure.  
    * Para obter mais informações e um exemplo, consulte [Quickstart: Crie uma aplicação web Core ASP.NET em Azure](../app-service/quickstart-dotnetcore.md). 
    * Para um tutorial detalhado sobre a criação de uma aplicação web e um ponto final, consulte [Tutorial: Conecte-se a uma aplicação web utilizando um Azure Private Endpoint](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network-and-bastion-host"></a>Criar uma rede virtual e hospedeiro de bastião

Nesta secção, você vai criar uma rede virtual, sub-rede e hospedeiro de bastião. 

O hospedeiro de bastião será utilizado para ligar de forma segura à máquina virtual para testar o ponto final privado.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso > Redes > Rede virtual** ou procure **Rede virtual** na caixa de pesquisa.

2. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | **Definição**          | **Valor**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Detalhes do projeto**  |                                                                 |
    | Subscrição     | selecione a subscrição do Azure                                  |
    | Grupo de Recursos   | Selecione **CreatePrivateEndpointqs-rg** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **Europa Ocidental**.|

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

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual compute de recurso ou procurar por máquina  >  **Compute**  >  **Virtual machine** **Virtual** na caixa de pesquisa.
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **CreatePrivateEndpointqs-rg** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM** |
    | Region | Selecione **Europa Ocidental**. |
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

## <a name="create-a-private-endpoint"></a>Criar um Ponto Final Privado

Nesta secção, irá criar um Ponto Final Privado para a aplicação web que criou na secção pré-requisitos.

1. No lado superior esquerdo do ecrã no portal, selecione **Criar um Link** Privado de Rede de Recursos , ou na caixa de pesquisa entrar No Link  >  **Networking**  >  **Private Link** **Privado**.

2. Selecione **Criar**.

3. No **Private Link Center**, selecione **Pontos finais privados** no menu da esquerda.

4. Em **pontos finais privados**, selecione **+ Adicionar**.

5. No separador **Básico de** Criar um ponto **final privado,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **CreatePrivateEndpointQs-rg**. Criou este grupo de recursos na secção anterior.|
    | **Detalhes da instância** |  |
    | Name  | Introduza **myPrivateEndpoint**. |
    | Region | Selecione **Europa Ocidental**. |

6. Selecione o **separador Recursos** ou o **seguinte: Botão de recurso** na parte inferior da página.
    
7. Em **Recurso,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Método de ligação | Selecione **Ligar a um recurso Azure no meu diretório**. |
    | Subscrição | Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Web/sites**. |
    | Recurso | Selecione **\<your-web-app-name>**. </br> Selecione o nome da aplicação web que criou nos pré-requisitos. |
    | Recurso secundário de destino | Selecione **sites**. |

8. Selecione o **separador Configuração** ou o **seguinte: Botão de configuração** na parte inferior do ecrã.

9. Na **Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | **Integração privada de DNS** |  |
    | Integrar com zona DNS privada | Deixe o padrão de **Sim**. |
    | Subscrição | Selecione a sua subscrição. |
    | Zonas DNS Privadas | Deixe o padrão de **(Nova) privatelink.azurewebsites.net**.
    

13. Selecione **Rever + criar**.

14. Selecione **Criar**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar à aplicação web através do ponto final privado.

1. Selecione **grupos** de recursos no painel de navegação à esquerda.

2. Selecione **CreatePrivateEndpointQs-rg**.

3. Selecione **myVM**.

4. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de ligar.

8. Introduza `nslookup <your-webapp-name>.azurewebsites.net`. **\<your-webapp-name>** Substitua-o pelo nome da aplicação web que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Um endereço IP privado de **10.1.0.5** é devolvido para o nome da aplicação web.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.

11. Na ligação de bastião ao **myVM,** abra o Internet Explorer.

12. Introduza o url da sua aplicação web, **https:// \<your-webapp-name> .azurewebsites.net**.

13. Receberá a página de aplicações web padrão se a sua aplicação não tiver sido implementada:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Página de aplicativo web padrão." border="true":::

18. Feche a ligação ao **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e a aplicação web com os seguintes passos:

1. A partir do menu da esquerda, selecione **Grupos de Recursos.**

2. Selecione **CreatePrivateEndpointQs-rg**.

3. Selecione **Eliminar grupo de recursos**.

4. **Insira CreatePrivateEndpointQS-rg** em **TIPO O NOME DO GRUPO DE RECURSOS**.

5. Selecione **Eliminar**.


## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Ponto final privado para uma App Web Azure.

Usou a máquina virtual para testar a conectividade de forma segura para a aplicação web em todo o ponto final privado.



Para obter mais informações sobre os serviços que suportam um ponto final privado, consulte:
> [!div class="nextstepaction"]
> [Disponibilidade de Ligação Privada](private-link-overview.md#availability)
