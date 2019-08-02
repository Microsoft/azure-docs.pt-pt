---
title: Integração de log do Azure com logs de auditoria Azure Active Directory | Microsoft Docs
description: Saiba como instalar o serviço de integração de logs do Azure e integrar logs de logs de auditoria do Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0820ca227a4d0e8c71ed3f35cd8fa2841163d38f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727656"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrar Azure Active Directory logs de auditoria

Os eventos de auditoria do Azure Active Directory (Azure AD) ajudam a identificar ações privilegiadas ocorridas no Azure Active Directory. Você pode ver os tipos de eventos que pode acompanhar examinando [Azure Active Directory eventos de relatório de auditoria](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Etapas para integrar Azure Active Directory logs de auditoria

> [!NOTE]
> Antes de tentar as etapas neste artigo, você deve examinar [o artigo de](azure-log-integration-get-started.md) introdução e concluir as etapas relevantes ali.

1. Abra o prompt de comando e execute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Execute este comando: 
 
   ``azlog createazureid``

   Esse comando solicita seu logon do Azure. O comando cria uma entidade de serviço Azure Active Directory nos locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação para o Azure é feita por meio do Azure AD. A criação de uma entidade de serviço para a integração de log do Azure cria a identidade do Azure AD que recebe acesso para leitura de assinaturas do Azure.

3. Execute o comando a seguir para fornecer sua ID de locatário. Você precisa ser membro da função de administrador de locatários para executar o comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do log de auditoria do Azure Active Directory foram criados neles:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

O vídeo a seguir demonstra as etapas abordadas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu sistema SIEM (gerenciamento de eventos e informações de segurança), entre em contato com seu fornecedor SIEM.

A assistência da Comunidade está disponível por meio do [Fórum do MSDN integração de logs do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na Comunidade de integração de logs do Azure ofereçam suporte entre si com perguntas, respostas, dicas e truques. Além disso, a equipe de integração de logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). Selecione **integração de log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a integração de log do Azure, consulte:

* [Integração de logs do Microsoft Azure para logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324): Esta página do centro de download fornece detalhes, requisitos do sistema e instruções de instalação para a integração do log do Azure.
* [Introdução à integração de log do Azure](azure-log-integration-overview.md): Este artigo apresenta a integração de log do Azure, seus principais recursos e como ele funciona.
* [Perguntas frequentes sobre integração de log do Azure](azure-log-integration-faq.md): Este artigo responde a perguntas sobre a integração de log do Azure.
* [Novos recursos para diagnóstico do Azure e logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que ajudam você a obter informações sobre as operações dos recursos do Azure.
