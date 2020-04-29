---
title: Faça upload de um Certificado de Gestão de Serviços Azure [ Microsoft Docs
description: Saiba como carregar o certificado de Gestão de Serviços para o portal Azure.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 4b49a9b391eeca2d2e249b171d99f231bda6fdff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78329124"
---
# <a name="upload-an-azure-service-management-certificate"></a>Faça upload de um Certificado de Gestão de Serviços Azure
Os certificados de gestão permitem-lhe autenticar com o modelo de implantação clássico fornecido pela Azure. Muitos programas e ferramentas (por exemplo, o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e a implementação de vários serviços do Azure. 

> [!WARNING]
> Cuidado! Este tipo de certificados permitem a qualquer pessoa que autentica com eles gerir a subscrição a que estão associados.
>
>

Se quiser mais informações sobre certificados Azure (incluindo a criação de um certificado auto-assinado), consulte a visão geral dos [Certificados para os Serviços de Nuvem Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Também pode utilizar o [Azure Ative Directory](https://azure.microsoft.com/services/active-directory/) para autenticar o código do cliente para fins de automação.

**Nota:** Deve ser coadministrador na subscrição para efetuar quaisquer operações ao abrigo de Certificados de Gestão. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=849300) sobre como adicionar ou remover coadministradores do novo Portal Azure 

## <a name="upload-a-management-certificate"></a>Faça upload de um certificado de gestão
Uma vez criado um certificado de gestão (.cer file com apenas a chave pública) pode carregá-lo no portal. Quando o certificado estiver disponível no portal, qualquer pessoa com um certificado de correspondência (chave privada) pode ligar através da API de Gestão e aceder aos recursos para a subscrição associada.

1. Faça login no [portal Azure.](https://portal.azure.com)
2. Clique em **Todos os serviços** na lista de serviços do Azure inferior e, em seguida, selecione **Subscrições** no grupo de serviço _geral._

    ![Menu de subscrição](./media/azure-api-management-certs/subscriptions_menu.png)

3. Certifique-se de selecionar a subscrição correta que pretende associar ao certificado.     
4. Depois de ter selecionado a subscrição correta, prima **os certificados** de Gestão no grupo _Definições._

    ![Definições](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Prima o botão **upload.**

    ![Upload na página de certificados](./media/azure-api-management-certs/certificates_page.png)
6. Preencha as informações de diálogo e prima **Carregar**.

    ![Definições](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Passos seguintes
Agora que tem um certificado de gestão associado a uma subscrição, pode (depois de ter instalado o certificado de correspondência localmente) ligar-se programáticamente ao modelo clássico de [implementação REST API](/azure/?pivot=sdkstools) e automatizar os vários recursos Azure que também estão associados a essa subscrição.
