---
title: Carregar um certificado de gestão de serviço do Azure | Documentos da Microsoft
description: Saiba como carregar o certificado de gestão de serviço para o portal do Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 3b6f1a004e4fb69bc08d5163734021bcb2aed9a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765803"
---
# <a name="upload-an-azure-service-management-certificate"></a>Carregar um certificado de gestão de serviço do Azure
Certificados de gestão permitem-lhe autenticar com o modelo de implementação clássico fornecido pelo Azure. Muitos programas e ferramentas (como o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e implementação dos vários serviços do Azure. 

> [!WARNING]
> Tenha cuidado! Esses tipos de certificados permitem que qualquer pessoa que efetua a autenticação com os mesmos para gerir a subscrição na que qual estão associados.
>
>

Se desejar obter mais informações sobre certificados do Azure (incluindo a criar um certificado autoassinado), veja [descrição geral de certificados para serviços Cloud do Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Também pode utilizar [do Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticar o código de cliente para fins de automatização.

**Nota:** Tem de ser um coadministrador na subscrição para realizar quaisquer operações em certificados de gestão. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=849300) sobre como adicionar ou remover Coadministradores do novo Portal do Azure 

## <a name="upload-a-management-certificate"></a>Carregar um certificado de gestão
Depois de ter um certificado de gestão que criou, (ficheiro. cer com apenas a chave pública) pode carregá-lo no portal. Quando o certificado está disponível no portal, qualquer pessoa com um certificado correspondente (chave privada) pode ligar através da API de gestão e aceder aos recursos para a subscrição associada.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **todos os serviços** na lista de serviço do Azure na parte inferior, em seguida, selecione **subscrições** no _geral_ grupo do serviço.

    ![Menu de subscrição](./media/azure-api-management-certs/subscriptions_menu.png)

3. Certifique-se selecionar a subscrição correta que pretende associar ao certificado.     
4. Depois de selecionar a subscrição correta, prima **certificados de gestão** no _definições_ grupo.

    ![Definições](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Prima a **carregar** botão.

    ![Carregar a página de certificados](./media/azure-api-management-certs/certificates_page.png)
6. Preencha as informações de caixa de diálogo e prima **carregar**.

    ![Definições](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Passos Seguintes
Agora que tem um certificado de gestão associado com uma subscrição, pode (depois de instalar o certificado correspondente localmente) por meio de programação ligar para o [REST API do modelo de implementação clássica](https://msdn.microsoft.com/library/azure/mt420159.aspx) e automatizar o vários recursos do Azure também estão associados essa subscrição.
