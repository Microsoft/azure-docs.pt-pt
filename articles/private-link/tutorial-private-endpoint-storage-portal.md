---
title: 'Tutorial: Ligue-se a uma conta de armazenamento usando um ponto final Azure Private'
titleSuffix: Azure Private Link
description: Começa com este tutorial usando o Azure Private endpoint para ligar a uma conta de armazenamento privada.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: eefef0eb61df9aa0597206b537f3d58e8b8b3213
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96484826"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Tutorial: Ligue-se a uma conta de armazenamento usando um Azure Private Endpoint

O ponto final privado Azure é o bloco de construção fundamental para private link em Azure. Permite que os recursos do Azure, como máquinas virtuais (VMs), comuniquem com os recursos de Private Link em privado.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma rede virtual e um hospedeiro de bastião.
> * Cria uma máquina virtual.
> * Crie uma conta de armazenamento com um ponto final privado.
> * Teste conectividade para armazenar conta privada ponto final.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

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
    | Grupo de Recursos | Selecione **myResourceGroup** |
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

## <a name="create-storage-account-with-a-private-endpoint"></a>Criar conta de armazenamento com um ponto final privado

Nesta secção, você vai criar uma conta de armazenamento e configurar o ponto final privado.

1. No menu à esquerda, selecione **Criar uma** conta de Armazenamento de Recursos  >    >  ou procurar conta **de Armazenamento** na caixa de pesquisa.

2. No separador Básicos da conta de **armazenamento** insira ou selecione as **seguintes** informações:

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da conta de armazenamento | **Insira a conta de mystorage.** Se o nome não estiver disponível, insira um nome único. |
    | Localização | Selecione **E.U.A. Leste**. |
    | Desempenho | Deixe o **Padrão padrão** |
    | Tipo de conta | Deixe o armazenamento predefinido **(finalidade geral v2)** |
    | Replicação| Deixe o **armazenamento geo-redundante de acesso de leitura predefinido (RA-GRS)** |
   
3. Selecione o **separador 'Rede'** ou selecione o botão **Seguinte: Botão de rede.**

4. No **separador 'Rede',** no **método de conectividade** selecione ponto final **privado**.

5. No **ponto final privado,** selecione **+ Adicionar**.

6. Na **Criação de ponto final privado** insira ou selecione as seguintes informações:

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | Localização | Selecione **E.U.A. Leste**. |
    | Name | Insira **o myPrivateEndpoint** |
    | Subresource de armazenamento | Deixe a **bolha** padrão |
    | **Rede** |  |
    | Rede virtual | Selecione **myVNet** |
    | Sub-rede | Selecione **mySubnet** |
    | **Integração privada de DNS** |
    | Integrar com zona DNS privada | Deixe o padrão **Sim** |
    | Zona DNS Privada | Deixe o padrão (Novo) privatelink.blob.core.windows.net |

7. Selecione **OK**.

8. Selecione **Rever + criar**.

9. Selecione **Criar**.

10. Selecione **grupos** de recursos no painel de navegação à esquerda.

11. Selecione **myResourceGroup**.

12. Selecione a conta de armazenamento que criou nos passos anteriores.

13. Na secção **Definições** da conta de armazenamento, selecione **as teclas de acesso**.

14. Selecione a cópia na **cadeia de ligação** para **a tecla1**.

## <a name="test-connectivity-to-private-endpoint"></a>Testar conectividade com o ponto final privado

Nesta secção, utilizará a máquina virtual que criou no passo anterior para ligar à conta de armazenamento através do ponto final privado.

1. Selecione **grupos** de recursos no painel de navegação à esquerda.

2. Selecione **myResourceGroup**.

3. Selecione **myVM**.

4. Na página geral do **myVM,** selecione **Connect** e, em **seguida, Bastion**.

5. Selecione o botão **azul Use Bastion.**

6. Introduza o nome de utilizador e a palavra-passe que introduziu durante a criação da máquina virtual.

7. Abra o Windows PowerShell no servidor depois de ligar.

8. Introduza `nslookup <storage-account-name>.blob.core.windows.net`. **\<storage-account-name>** Substitua-o pelo nome da conta de armazenamento que criou nos passos anteriores.  Receberá uma mensagem semelhante à que é exibida abaixo:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Um endereço IP privado de **10.1.0.5** é devolvido para o nome da conta de armazenamento.  Este endereço encontra-se na sub-rede da rede virtual que criou anteriormente.

9. Instale o [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) na máquina virtual.

10. **Selecione Terminar** depois de instalado o **Microsoft Azure Storage Explorer.**  Deixe a caixa verificada para abrir o pedido.

11. No ecrã **de armazenamento "Ligar ao Azure",** selecione **Utilize uma cadeia de ligação**.

12. Selecione **Seguinte**.

13. Introduza o nome da sua conta de armazenamento a partir dos passos anteriores no **nome Display**.

14. Na caixa em **'Connection String',** cole o fio de ligação da conta de armazenamento que copiou nos passos anteriores.

15. Selecione **Seguinte**.

16. Verifique se as definições estão corretas no **Resumo da Ligação**.  

17. Selecione **Ligar**.

18. Feche a ligação ao **myVM**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine a rede virtual, a máquina virtual e a conta de armazenamento com os seguintes passos:

1. A partir do menu da esquerda, selecione **Grupos de Recursos.**

2. Selecione **myResourceGroup**.

3. Selecione **Eliminar grupo de recursos**.

4. Introduza **o myResourceGroup** em **TIPO O NOME DO GRUPO DE RECURSOS**.

5. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Saiba como criar um serviço de Ligação Privada:
> [!div class="nextstepaction"]
> [Criar um serviço de Ligação Privada](create-private-link-service-portal.md)