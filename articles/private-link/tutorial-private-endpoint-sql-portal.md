---
title: 'Tutorial: Conecte-se a um servidor Azure SQL utilizando um Azure Private Endpoint - Portal'
description: Utilize este tutorial para aprender a criar um servidor Azure SQL com um ponto final privado utilizando o portal Azure.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8bb227bdc4bbcdf986c2ed64e1f89e1dfa289b83
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554977"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Tutorial: Conecte-se a um servidor Azure SQL utilizando um Azure Private Endpoint - Portal Azure

O ponto final privado Azure é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem com os recursos de Private Link em privado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual e um hospedeiro de bastião.
> * Cria uma máquina virtual.
> * Crie um servidor Azure SQL e um ponto final privado.
> * Teste a conectividade com o ponto final privado do servidor SQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

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
    | Grupo de Recursos   | Selecione **CreateSQLEndpointTutorial-rg** |
    | **Detalhes da instância** |                                                                 |
    | Name             | Insira **myVNet**                                    |
    | Region           | Selecione **E.U.A. Leste**. |

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
    | Grupo de Recursos | Selecione **CreateSQLEndpointTutorial** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM** |
    | Region | Selecione **E.U.A. Leste**. |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Criar um servidor Azure SQL e ponto final privado

Nesta secção, irá criar um servidor SQL em Azure. 

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar uma** base de  >  dados SQL **de bases de dados de** recursos  >  .

1. No separador **Básico da** base de **dados Create SQL,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **CreateSQLEndpointTutorial**. Criou este grupo de recursos na secção anterior.|
    | **Detalhes da base de dados** |  |
    | Nome da base de dados  | Insira **a base de dados mysql**. Se este nome for tomado, crie um nome único. |
    | Servidor | Selecione **Criar novo**. |

6. No **novo servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do servidor  | Insira **mysqlserver**. Se este nome for tomado, crie um nome único.|
    | Início de sessão de administrador do servidor | Insira o nome de administrador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 8 caracteres de comprimento e satisfazer os requisitos definidos. |
    | Localização | Selecione **E.U.A. Leste**. |
    
7. Selecione **OK**.

8. Selecione o **separador 'Rede'** ou selecione o botão **Seguinte: Botão de rede.**

9. No **separador Networking,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Conectividade de rede** | |
    | Método de conectividade | Selecione **Ponto final privado**. |
   
10. **Selecione + Adicione o ponto final privado** em **pontos finais privados**.

11. Em **Criar ponto final privado,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **CreateSQLEndpointTutorial**. |
    | Localização | Selecione **East US**. |
    | Name | Insira **o myPrivateSQLendpoint**. |
    | Recurso secundário de destino | Selecione **SQLServer**. |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet**. |
    | **Integração privada de DNS** | |
    | Integrar com zona DNS privada | Deixe o padrão **Sim**. |
    | Zona DNS Privada | Deixe o **padrão (Novo) privatelink.database.windows.net**. |

12. Selecione **OK**. 

13. Selecione **Rever + criar**.

14. Selecione **Criar**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar ao servidor SQL através do ponto final privado.

1. Selecione **grupos** de recursos no painel de navegação à esquerda.

2. Selecione **CreateSQLEndpointTutorial**.

3. Selecione **myVM**.

4. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de ligar.

8. Introduza `nslookup <sqlserver-name>.database.windows.net`. **\<sqlserver-name>** Substitua-o pelo nome do servidor SQL que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Um endereço IP privado de **10.1.0.5** é devolvido para o nome do servidor SQL.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.


9. Instale [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) no **myVM**.

10. Open **SQL Server Management Studio**.

4. Em **Ligar ao servidor,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Tipo de servidor | Selecione **Motor de Base de Dados**.|
    | Nome do servidor | Insira **\<sqlserver-name> .database.windows.net** |
    | Autenticação | Selecione **SQL Server Authentication** (Autenticação do SQL Server). |
    | Nome de utilizador | Introduza o nome de utilizador que inseriu durante a criação do servidor |
    | Palavra-passe | Introduza a palavra-passe que introduziu durante a criação do servidor |
    | Memorizar palavra-passe | Selecione **Yes** (Sim). |

1. Selecione **Ligar**.
2. Procure bases de dados a partir do menu esquerdo.
3. (Opcionalmente) Criar ou consultar informações a partir da **mysqldatabase**.
4. Feche a ligação remota do ambiente de trabalho ao **myVM**. 

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o ponto final privado, o servidor SQL e o VM, elimine o grupo de recursos e todos os recursos que contém: 
1. Introduza **CreateSQLEndpointTutorial** na caixa **de pesquisa** no topo do portal e selecione **CreateSQLEndpointTutorial** a partir dos resultados da pesquisa. 
2. Selecione **Eliminar grupo de recursos**. 
3. Introduza CreateSQLEndpointTutorial para **TYPE THE RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um:

* Rede virtual e hospedeiro de bastião.
* Máquina virtual.
* Servidor Azure SQL com ponto final privado.

Utilizou a máquina virtual para testar a conectividade de forma segura para o servidor SQL através do ponto final privado.

Como próximo passo, poderá também estar interessado na aplicação Web com conectividade privada ao cenário de arquitetura **de base de dados Azure SQL,** que liga uma aplicação web fora da rede virtual ao ponto final privado de uma base de dados.
> [!div class="nextstepaction"]
> [Aplicativo web com conectividade privada para base de dados Azure SQL](/azure/architecture/example-scenario/private-web-app/private-web-app)