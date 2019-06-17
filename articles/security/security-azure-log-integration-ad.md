---
title: Integração de registos do Azure com registos de auditoria do Azure Active Directory | Documentos da Microsoft
description: Saiba como instalar o serviço de integração de registos do Azure e integrar registos do registos de auditoria do Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 6e99553afc05fca0250607bfccd2799adaf471e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298165"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrar registos de auditoria do Azure Active Directory

Eventos de auditoria do Azure Active Directory (Azure AD) a identificar ações privilegiadas que ocorreram no Azure Active Directory. Pode ver os tipos de eventos que pode controlar ao rever [eventos de relatório de auditoria do Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> A funcionalidade de integração de registo do Azure vai ser preterida até 15/06/2019. Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Registos de auditoria de passos para integrar o Azure Active Directory

> [!NOTE]
> Antes de tentar os passos neste artigo, tem de rever o [começar](security-azure-log-integration-get-started.md) artigo e conclua os passos relevantes lá.

1. Abra a linha de comandos e execute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Execute este comando: 
 
   ``azlog createazureid``

   Este comando pede-lhe o início de sessão do Azure. O comando, em seguida, cria um Azure Active Directory principal de serviço em inquilinos do Azure AD que alojam as subscrições do Azure em que o utilizador com sessão iniciada é um administrador, coadministrador ou um proprietário. O comando irá falhar se o utilizador com sessão iniciada é apenas um utilizador convidado no inquilino do Azure AD. Autenticação para o Azure é feita através do Azure AD. Criar um principal de serviço para o Azure Log Integration cria a identidade do Azure AD que é dado acesso ao ler a partir de subscrições do Azure.

3. Execute o seguinte comando para fornecer sua ID do inquilino. Tem de ser membro da função de administrador do inquilino para executar o comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verifique as seguintes pastas para confirmar que os ficheiros JSON de registo de auditoria do Azure Active Directory são criados nos mesmos:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

O vídeo seguinte demonstra as etapas abordadas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obter instruções específicas sobre trazer as informações dos ficheiros JSON para suas informações de segurança e o sistema de gestão (SIEM) de eventos, contacte o fornecedor SIEM.

Assistência de Comunidade está disponível através da [fórum MSDN do Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na Comunidade do Azure Log Integration para suportar entre si com perguntas, respostas, dicas e truques. Além disso, a equipa de integração de registos do Azure monitoriza este Fórum e ajuda a sempre que possível.

Também pode abrir um [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Selecione **integração de registos** como o serviço para o qual está a solicitar suporte.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a integração de registo do Azure, veja:

* [Para os registos do Azure do Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324): Esta página do Centro de transferências fornece detalhes e instruções de instalação para o Azure Log Integration requisitos do sistema.
* [Introdução à integração de registos do Azure](security-azure-log-integration-overview.md): Este artigo apresenta o Azure Log Integration, suas principais capacidades e como ele funciona.
* [Integração de registos do Azure FAQ](security-azure-log-integration-faq.md): Este artigo responde a perguntas sobre a integração de registos do Azure.
* [Registos de auditoria de novas funcionalidades para o diagnóstico do Azure e Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Esta postagem de blog apresenta os registos de auditoria do Azure e outros recursos que o ajudam a obter informações sobre as operações de recursos do Azure.
