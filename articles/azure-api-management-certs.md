---
title: Carregar um certificado de gerenciamento de serviços do Azure | Microsoft Docs
description: Saiba como carregar o certificado de gerenciamento de serviços para o portal do Azure.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 5ce96796cbfdcefbaf5568ff199cba6a87f65e05
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359871"
---
# <a name="upload-an-azure-service-management-certificate"></a>Carregar um certificado de gerenciamento de serviços do Azure
Os certificados de gerenciamento permitem autenticar com o modelo de implantação clássico fornecido pelo Azure. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) usam esses certificados para automatizar a configuração e a implantação de vários serviços do Azure. 

> [!WARNING]
> Tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que se autentique com eles gerencie a assinatura à qual eles estão associados.
>
>

Se você quiser obter mais informações sobre os certificados do Azure (incluindo a criação de um certificado autoassinado), consulte [visão geral de certificados para serviços de nuvem do Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Você também pode usar [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticar o código do cliente para fins de automação.

**Nota:** Você deve ser um coadministrador na assinatura para executar qualquer operação em certificados de gerenciamento. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=849300) sobre como adicionar ou remover coadministradores do novo portal do Azure 

## <a name="upload-a-management-certificate"></a>Carregar um certificado de gerenciamento
Depois de criar um certificado de gerenciamento (arquivo. cer com apenas a chave pública), você poderá carregá-lo no Portal. Quando o certificado estiver disponível no portal, qualquer pessoa com um certificado de correspondência (chave privada) poderá se conectar por meio da API de gerenciamento e acessar os recursos para a assinatura associada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **todos os serviços** na lista inferior de serviços do Azure e, em seguida, selecione **assinaturas** no grupo de serviços _geral_ .

    ![Menu assinatura](./media/azure-api-management-certs/subscriptions_menu.png)

3. Certifique-se de selecionar a assinatura correta que você deseja associar ao certificado.     
4. Depois de selecionar a assinatura correta, pressione **certificados de gerenciamento** no grupo _configurações_ .

    ![Definições](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Pressione o botão **carregar** .

    ![Carregar na página certificados](./media/azure-api-management-certs/certificates_page.png)
6. Preencha as informações da caixa de diálogo e pressione **carregar**.

    ![Definições](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que você tem um certificado de gerenciamento associado a uma assinatura, você pode (depois de ter instalado o certificado correspondente localmente) se conectar programaticamente à [API REST do modelo de implantação clássica](/azure/#pivot=sdkstools) e automatizar os vários recursos do Azure que também estão associados a essa assinatura.
