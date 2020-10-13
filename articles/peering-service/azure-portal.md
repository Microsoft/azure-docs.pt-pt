---
title: Registre o Serviço de Observação Azure - Portal Azure
description: Saiba como registar o Serviço de Observação Azure utilizando o portal Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534952"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Registre-se serviço de peering usando o portal Azure

O Azure Peering Service é um serviço de networking que melhora a conectividade do cliente com os serviços na nuvem da Microsoft, tais como o Microsoft 365, Dynamics 365, software como serviço (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis através da internet pública.

Neste artigo, você vai aprender a registar uma ligação de Serviço de Peering usando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

> 

## <a name="prerequisites"></a>Pré-requisitos

Deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Deve ter uma conta Microsoft Azure válida e ativa. Esta conta é necessária para configurar a ligação serviço de peering. O Serviço de Observação é um recurso dentro das subscrições Azure. 

### <a name="connectivity-provider"></a>Fornecedor de conectividade

Pode trabalhar com um fornecedor de serviços de internet ou um parceiro de intercâmbio de internet para obter o Peering Service para ligar a sua rede à rede Microsoft.

Certifique-se de que os [fornecedores de conectividade](location-partners.md) são parceiros com a Microsoft.



## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A partir de um browser, vá ao portal Azure e inscreva-se na sua conta Azure.

## <a name="register-a-peering-service-connection"></a>Registe uma ligação ao Peering Service

1. Para registar uma ligação de Serviço de Peering, **selecione Criar um**serviço  >  **de peering de**recursos .

    ![Registar Peering Service](./media/peering-service-portal/peering-servicecreate.png)
1. Introduza os seguintes detalhes no separador **Básicos** na página Criar uma página **de conexão de serviço de espreitar.**

 
1. Selecione a subscrição e o grupo de recursos associado à subscrição.

   ![Registar Guia de peering](./media/peering-service-portal/peering-servicebasics.png)

1. Introduza um **Nome** ao qual deve ser registada a instância do Serviço de Observação.
 
1. Agora, selecione o botão **Seguinte:Configuração** na parte inferior da página. A página **de Configuração** aparece.

## <a name="configure-the-peering-service-connection"></a>Configurar a ligação de Serviço de Peering

1. Na página **Configuração,** selecione o local ao qual o Serviço de Peering deve ser ativado selecionando o mesmo na lista de localização do **serviço de peering.**

1. Selecione o prestador de serviços a partir do qual o Serviço de Pares deve ser obtido selecionando um nome de fornecedor da lista de down-down do prestador de **serviços Peering.**
 
1. **Selecione Criar novo prefixo** na parte inferior da secção **Prefixos** e aparecer caixas de texto. Agora, insira o nome do recurso prefixo e os prefixos que estão associados ao prestador de serviços.

1. Selecione **a Chave Prefixo** e adicione a Chave prefixo que lhe foi dada pelo seu fornecedor (ISP ou IXP). Esta chave permite aos Ms validar o prefixo e o fornecedor que alocou o seu prefixo IP.
   > ![A screenshot mostra o separador Configuração da página de ligação de serviço de espreitar onde pode introduzir a tecla Prefix.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecione o botão **'Rever +'** no lado inferior esquerdo da página. Aparece a página **Review + create** e o Azure valida a sua configuração.
    

1. Quando vir a mensagem **de validação passada** como mostrado, selecione **Criar**.

   > ![Screenshot mostra o separador Review + criar uma página de conexão de serviço de espreitar.](./media/peering-service-portal/peering-service-prefix.png)


1. Depois de registar uma ligação de Serviço de Peering, é realizada uma validação adicional nos prefixos incluídos. Pode rever o estado de validação na secção **Prefixos** do nome do recurso. Se a validação falhar, é apresentada uma das seguintes mensagens de erro:

   - Prefixo de Serviço de Pares Inválido, o prefixo deve ser um formato válido, apenas o prefixo Ipv4 é suportado.
   - O prefixo não foi recebido pelo prestador de serviços peering.
   - O anúncio do prefixo não tem uma comunidade BGP válida, contacte o prestador de serviços peering.
   - Rota de backup não encontrada, contacte o fornecedor de Serviços Peering.
   - Prefixo recebido com caminho AS mais longo, contacte o prestador de serviços peering.
   - Prefixo recebido com AS privado no caminho, contacte o prestador de serviços peering.

### <a name="add-or-remove-a-prefix"></a>Adicionar ou remover um prefixo

**Selecione Adicionar prefixos** na página **Prefixos** para adicionar prefixos.

Selecione a elipse (...) ao lado do prefixo listado e selecione a opção **Eliminar.**

### <a name="delete-a-peering-service-connection"></a>Eliminar uma ligação de Serviço de Peering

Na página **Todos os Recursos,** selecione a caixa de verificação no Serviço de Observação e selecione a opção **Eliminar** no topo da página.

> [!NOTE]
> Não se pode modificar um prefixo existente.
>

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação do Serviço de Peering, consulte [a ligação do Serviço de Peering](connection.md).
- Para saber mais sobre a telemetria de conexão peering Service, consulte [a telemetria de conexão peering service](connection-telemetry.md).
- Para medir a telemetria, consulte [a telemetria de ligação medida](measure-connection-telemetry.md).
- Para registar a ligação utilizando a Azure PowerShell, consulte [registar uma ligação de Serviço de Pares - Azure PowerShell](powershell.md).
- Para registar a ligação utilizando o Azure CLI, consulte [registar uma ligação de Serviço de Pares - Azure CLI](cli.md).
