---
title: Contas de serviço do Azure Ative Directory
description: Princípios e procedimentos para a gestão do ciclo de vida das contas de serviço no Azure Ative Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f540ab40a14af09aa8667860286021f572eb6f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587904"
---
# <a name="governing-azure-ad-service-accounts"></a>Contas de serviço AZure AD governando

Existem três tipos de contas de serviço no Azure Ative Directory (Azure AD): [identidades geridas,](service-accounts-managed-identities.md) [principais serviços](service-accounts-principal.md)e contas de utilizador empregadas como contas de serviço. Ao criar estas contas de serviço para uso automatizado, são-lhes concedidas permissões de acesso a recursos em Azure e Azure AD. Os recursos podem incluir serviços Microsoft 365, software como aplicações de serviço (SaaS), aplicações personalizadas, bases de dados, sistemas de RH, etc. As contas de serviço Azure AD que regem o serviço Azure significa que gere a sua criação, permissões e ciclo de vida para garantir a segurança e a continuidade.

> [!IMPORTANT] 
> Não recomendamos a utilização de contas de utilizador como contas de serviço, uma vez que são inerentemente menos seguras. Isto inclui contas de serviço no local sincronizadas com a Azure AD, uma vez que não são convertidas em diretores de serviço. Em vez disso, recomendamos a utilização de identidades geridas ou princípios de serviço. Note que neste momento o uso de políticas de acesso condicional não é possível com os principais serviços, mas a funcionalidade está a chegar.


## <a name="plan-your-service-account"></a>Planeie a sua conta de serviço

Antes de criar uma conta de serviço, ou de registar uma aplicação, documente as informações-chave da conta de serviço. Ter informação documentada facilita o acompanhamento e a gestão eficaz da conta. Recomendamos recolher os seguintes dados e rastreá-lo na sua Base de Dados centralizada de Gestão de Configuração (CMDB).

| Dados| Description| Detalhes |
| - | - | - |
| Proprietário| Utilizador ou grupo responsável pela gestão e monitorização da conta de serviço.| Doar ao proprietário as permissões necessárias para monitorizar a conta e implementar uma forma de mitigar as questões. A mitigação da questão pode ser feita pelo proprietário, ou através de um pedido de TI. |
| Objetivo| Como a conta será usada.| Mapear a conta de serviço para um serviço específico, aplicação ou script. Evite criar contas de serviço multiusos. |
| Permissões (Âmbitos)| Conjunto antecipado de permissões.| Documente os recursos a que terá acesso e as permissões a esses recursos. |
| Ligação CMDB| Ligue-se aos recursos a aceder e aos scripts nos quais a conta de serviço é utilizada.| Certifique-se de que documenta os proprietários de recursos e scripts para que possa comunicar os efeitos necessários a montante e a jusante das alterações. |
| Avaliação de riscos| Risco e impacto do negócio se a conta fosse comprometida.| Utilize estas informações para reduzir o âmbito das permissões e determinar quem deve ter acesso à informação da conta. |
| Período de revisão| O horário em que a conta de serviço deve ser revista pelo proprietário.| Use isto para agendar comunicações e comentários de revisão. Documente o que deve acontecer se um reexame não for realizado por um tempo específico após o período de revisão programado. |
| Vida útil| Antecipação de vida útil da conta.| Utilize isto para agendar comunicações para o proprietário e, em última análise, desative as contas. Sempre que possível, estabeleça uma data de validade para as credenciais, onde as credenciais não possam ser reroladas automaticamente. |
| Name| Nome de conta padronizado| Crie um esquema de nomeação para todas as contas de serviço para que possa facilmente pesquisar, classificar e filtrar nas contas de serviço. |


## <a name="use-the-principle-of-least-privileges"></a>Use o princípio dos privilégios menos utilizados
Conceder à conta de serviço apenas as permissões necessárias para executar as suas tarefas, e nada mais. Se uma conta de serviço precisar de permissões de alto nível, por exemplo um nível global de privilégio de administrador, avalie o porquê e tente reduzir as permissões necessárias.

Recomendamos as seguintes práticas para privilégios de conta de serviço.

**Permissões**

* Não atribuir funções incorporadas a contas de serviço. Em vez disso, utilize o [modelo de concessão de permissão OAuth2 para o Microsoft Graph](/graph/api/resources/oauth2permissiongrant),

* Se o diretor de serviço tiver de ser atribuído a um papel privilegiado, considere atribuir um [papel personalizado](../roles/custom-create.md) com privilégios específicos e necessários, de forma limitada ao tempo.

* Não inclua contas de serviço como membros de quaisquer grupos com permissões elevadas. 

* [Use o PowerShell para enumerar membros de funções privilegiadas,](/powershell/module/azuread/get-azureaddirectoryrolemember)tais como   
‎ `Get-AzureADDirectoryRoleMember` , and filter for objectType "Service Principal".

   ou usar  
‎   `Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Utilize os âmbitos OAuth 2.0](../develop/v2-permissions-and-consent.md) para limitar a funcionalidade que uma conta de serviço pode aceder a um recurso.
* Os principais serviços e identidades geridas podem utilizar os âmbitos OAuth 2.0 num contexto delegado que se esteja a fazer passar por um utilizador assinado ou como conta de serviço no contexto da aplicação. No contexto da candidatura não está assinado.

* Verifique o pedido de recursos das contas de serviço de âmbito para garantir que são apropriados. Por exemplo, se uma conta estiver a solicitar Ficheiros.ReadWrite.All, avalie se realmente precisa apenas de File.Read.All. Para obter mais informações sobre permissões, consulte a referência de [permissão do Microsoft Graph](/graph/permissions-reference).

* Certifique-se de que confia no desenvolvedor da aplicação ou na API com o acesso solicitado aos seus recursos.

**Duration**

* Limite as credenciais de conta de serviço (segredo do cliente, certificado) a um período de utilização previsto.

* Agendar revisões periódicas da utilização e finalidade das contas de serviço. Certifique-se de que as avaliações são realizadas antes do termo da conta.

Assim que tiver uma compreensão clara da finalidade, âmbito e permissões necessárias, crie a sua conta de serviço. 

[Criar e utilizar identidades geridas](../../app-service/overview-managed-identity.md?tabs=dotnet)

[Criar e utilizar os principais de serviço](../develop/howto-create-service-principal-portal.md)

Use uma identidade gerida sempre que possível. Se não puder utilizar uma identidade gerida, utilize um diretor de serviço. Se não puder utilizar um titular de serviço, então e só então utilize uma conta de utilizador Azure.

 

## <a name="build-a-lifecycle-process"></a>Construir um processo de ciclo de vida

Gerir o ciclo de vida de uma conta de serviço começa com o planeamento e termina com a sua eliminação permanente. 

Este artigo já abordou previamente a parte de planeamento e criação. Também deve monitorizar, rever permissões, determinar o uso continuado de uma conta e, em última análise, desprovisionar a conta.

### <a name="monitor-service-accounts"></a>Monitorizar contas de serviço

Monitorize proativamente as suas contas de serviço para garantir que os padrões de utilização da conta de serviço refletem os padrões pretendidos e que a conta de serviço ainda é utilizada ativamente.

**Recolher e monitorizar os insuposições de conta de serviço utilizando um dos seguintes métodos:**

* Utilizando o Azure AD Sign-In Logs no Portal AD Azure.

* Exportando o Azure AD Sign-In Logs para [Azure Storage](../../storage/index.yml), [Azure Event Hubs,](../../event-hubs/index.yml)ou [Azure Monitor](../../azure-monitor/logs/data-platform-logs.md).


![Imagem de tela mostrando o ecrã principal de inscrição do serviço.](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**A inteligência que deve procurar nos registos Sign-In inclui:**

* Há contas de serviço que já não assinam o inquilino?

* Os padrões de inscrição das contas de serviço estão a mudar?

Recomendamos que exporte registos de login Azure AD e os importe para as ferramentas existentes de Informação de Segurança e Gestão de Eventos (SIEM), como o Azure Sentinel. Utilize o seu SIEM para construir alertas e painéis de instrumentos.

### <a name="review-service-account-permissions"></a>Rever permissões de conta de serviço

Reveja regularmente as permissões concedidas e os âmbitos acedidos por contas de serviço para ver se podem ser eliminados.

* Utilize [o PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) para [criar automatização para verificar e documentar](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) âmbitos aos quais o consentimento é concedido a uma conta de serviço.

* Utilize o PowerShell para [rever as credenciais dos principais do serviço existentes](https://github.com/AzureAD/AzureADAssessment) e verificar a sua validade.

* Não desemoque as credenciais do diretor de serviço para "Nunca expire".

* Utilize certificados ou credenciais armazenados em Azure KeyVault sempre que possível.

A amostra gratuita da Microsoft PowerShell recolhe as subvenções e informações credenciais OAuth2 do diretor do serviço, regista-as num ficheiro de valores separados por vírgula (CSV) e num painel de amostras power BI para interpretar e utilizar os dados. para obter mais informações, consulte [AzureAD/AzureADAssessment: Ferramenta para avaliar um estado e configuração de inquilinos Azure AD (github.com)](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Recertifique o uso da conta de serviço

Estabeleça um processo de revisão para garantir que as contas de serviço são regularmente revistas pelos seus proprietários e pela equipa de segurança ou TI a intervalos regulares. 

**O processo deve incluir:**

* Como determinar o ciclo de revisão de cada conta de serviço (deve ser documentado no seu CMDB).

* As comunicações para as equipas de proprietários e de segurança ou de TI antes do início das revisões.

* O tempo e o conteúdo das comunicações de aviso se a revisão for perdida.

* Instruções sobre o que fazer se os proprietários não reverem ou responderem. Por exemplo, pode querer desativar (mas não apagar) a conta até que a revisão esteja concluída.

* Instruções para determinar as dependências a montante e a jusante e notificar os outros proprietários de recursos de quaisquer efeitos.

**A revisão deve incluir o proprietário e o seu parceiro de TI certificando que:**

* A conta ainda é necessária.

* As permissões concedidas à conta são adequadas e necessárias, ou é solicitada uma alteração.

* O acesso à conta e as suas credenciais são controlados.

* As credenciais que as utilizações da conta são adequadas, no que diz respeito ao risco com que a conta foi avaliada (tipo credencial e vida útil credencial)

* A pontuação de risco da conta não mudou desde a última recertificação

* Uma atualização sobre a vida útil esperada da conta, e a próxima data de recertificação.

### <a name="deprovision-service-accounts"></a>Contas de serviço de desprovisionamento

**Contas de serviço deprovision nas seguintes circunstâncias:****

* O script ou aplicação para o qual a conta de serviço foi criada está aposentado.

* A função dentro do script ou aplicação para a qual a conta de serviço é utilizada (por exemplo, o acesso a um recurso específico) é reformada.


* A conta de serviço é substituída por uma conta de serviço diferente.

* As credenciais expiraram, ou a conta não é funcional, e não há reclamações.

**Os processos de desprovisionamento devem incluir as seguintes tarefas.**

1. Uma vez desprovisionada a aplicação ou script associado, [monitorize as entradas](../reports-monitoring/concept-sign-ins.md#sign-ins-report) e o acesso aos recursos pela conta de serviço.

   * Se a conta ainda estiver ativa, determine como está a ser usada antes de tomar os passos subsequentes.
 
2. Se esta é uma identidade de serviço gerida, então desative a conta de serviço de iniciar sessão, mas não a remova do diretório.

3. Revogar as atribuições de funções e as concessões de consentimento da OAuth2 para a conta de serviço.

4. Após um período definido e um amplo aviso aos proprietários, apague a conta de serviço do diretório.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a segurança das contas de serviço da Azure, consulte:

[Introdução às contas de serviço da Azure](service-accounts-introduction-azure.md)

[Assegurar identidades geridas](service-accounts-managed-identities.md)

[Assegurar princípios de serviço](service-accounts-principal.md)




 

