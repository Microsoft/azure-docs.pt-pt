---
title: Lista de verificação de segurança operacional do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de lista de verificação para a segurança operacional do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 3ac414ddd9a59154beadd60132393be8f8dfde98
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035999"
---
# <a name="azure-operational-security-checklist"></a>Lista de verificação de segurança operacional do Azure
Implantar um aplicativo no Azure é rápido, fácil e econômico. Antes de implantar o aplicativo de nuvem em produção, é útil ter uma lista de verificação para ajudar a avaliar seu aplicativo em relação às ações de segurança operacionais essenciais e recomendadas a serem consideradas.

## <a name="introduction"></a>Introdução

O Azure fornece um pacote de serviços de infraestrutura que você pode usar para implantar seus aplicativos. A segurança operacional do Azure refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure.

-   Para obter o benefício máximo da plataforma de nuvem, recomendamos que você aproveite os serviços do Azure e siga a lista de verificação.
-   As organizações que investem em tempo e recursos avaliando a prontidão operacional de seus aplicativos antes do lançamento têm uma taxa muito mais alta de satisfação do que aquelas que não têm. Ao executar esse trabalho, as listas de verificação podem ser um mecanismo inestimável para garantir que os aplicativos sejam avaliados de forma consistente e holística.
-   O nível de avaliação operacional varia dependendo do nível de maturidade da nuvem da organização e da fase de desenvolvimento do aplicativo, das necessidades de disponibilidade e dos requisitos de confidencialidade de dados.

## <a name="checklist"></a>Lista de verificação

Esta lista de verificação destina-se a ajudar as empresas a imaginar várias considerações de segurança operacional à medida que implantam aplicativos empresariais sofisticados no Azure. Ele também pode ser usado para ajudá-lo a criar uma estratégia de operação e migração de nuvem segura para sua organização.

|Categoria da lista de verificação| Descrição|
| ------------ | -------- |
| [<br>Funções de segurança & controles de acesso](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Use o [RBAC (controle de acesso baseado em função)](../../role-based-access-control/role-assignments-portal.md) para fornecer uma específica ao usuário que costumava atribuir permissões a usuários, grupos e aplicativos em um determinado escopo.</li></ul> |
| [<br>Coleta de dados & armazenamento](../../storage/common/storage-security-guide.md)|<ul><li>Use a segurança do plano de gerenciamento para proteger sua conta de armazenamento usando o [RBAC (controle de acesso baseado em função)](../../role-based-access-control/role-assignments-portal.md).</li><li>Segurança do plano de dados para proteger o acesso aos seus dados usando [SAS (assinaturas de acesso compartilhado)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) e políticas de acesso armazenadas.</li><li>Use a criptografia em nível de transporte – usando HTTPS e a criptografia usada pelo [SMB (protocolos de protocolo de mensagens de servidor) 3,0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) para compartilhamentos de [arquivos do Azure](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Use a [criptografia do lado do cliente](../../storage/common/storage-client-side-encryption.md) para proteger os dados enviados para contas de armazenamento quando você precisar de controle exclusivo de chaves de criptografia. </li><li>Use o [criptografia do serviço de armazenamento (SSE)](../../storage/common/storage-service-encryption.md) para criptografar automaticamente os dados no armazenamento do Azure e [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) para criptografar os arquivos de disco da máquina virtual para o sistema operacional e os discos de dados.</li><li>Usar o [análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) do Azure para monitorar o tipo de autorização; assim como no armazenamento de BLOBs, você pode ver se os usuários usaram uma assinatura de acesso compartilhado ou as chaves da conta de armazenamento.</li><li>Use o [CORS (compartilhamento de recursos entre origens)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) para acessar recursos de armazenamento de domínios diferentes.</li></ul> |
|[<br>Recomendações de & de políticas de segurança](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Use a [central de segurança do Azure](../../security-center/security-center-install-endpoint-protection.md) para implantar soluções de ponto de extremidade.</li><li>Adicione um [WAF (firewall do aplicativo Web)](../../application-gateway/waf-overview.md) para proteger aplicativos Web.</li><li>   Use um [Firewall](../../sentinel/connect-data-sources.md) de um parceiro da Microsoft para aumentar suas proteções de segurança. </li><li>Aplicar detalhes de contato de segurança para sua assinatura do Azure; Esse [MSRC (Microsoft Security Response Center)](https://technet.microsoft.com/security/dn528958.aspx) o contata se descobrir que os dados do cliente foram acessados por uma parte ilegal ou não autorizada.</li></ul> |
| [<br>Gerenciamento de Identidades e Acesso](identity-management-best-practices.md)|<ul><li>[Sincronize seu diretório local com seu diretório de nuvem usando o Azure ad](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Use o [logon único](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) para permitir que os usuários acessem seus aplicativos SaaS com base em suas contas organizacionais no Azure AD.</li><li>Use o relatório [atividade de registro](../../active-directory/active-directory-passwords-reporting.md) de redefinição de senha para monitorar os usuários que estão se registrando.</li><li>Habilite a [autenticação multifator (MFA)](../../active-directory/authentication/multi-factor-authentication.md) para usuários.</li><li>Os desenvolvedores usem recursos de identidade seguros para aplicativos como [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Monitore ativamente as atividades suspeitas usando Azure AD Premium relatórios de anomalias e a [capacidade do Azure ad Identity Protection](../../active-directory/identity-protection/overview.md).</li></ul> |
|[<br>Monitoramento de segurança em andamento](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Use Avaliação de Malware solução [Azure monitor logs](../../log-analytics/log-analytics-queries.md) para relatar o status da proteção antimalware em sua infraestrutura.</li><li>Use a [avaliação de atualização](../../automation/automation-update-management.md) para determinar a exposição geral a problemas potenciais de segurança e se ou qual é a importância dessas atualizações para o seu ambiente.</li><li>A [identidade e o acesso](../../security-center/security-center-monitoring.md) fornecem uma visão geral do usuário </li><ul><li>Estado de identidade do usuário,</li><li>número de tentativas com falha de entrar,</li><li> a conta do usuário que foi usada durante essas tentativas, contas que foram bloqueadas</li> <li>contas com senha alterada ou redefinida </li><li>Número atual de contas que estão conectadas.</li></ul></ul> |
| [<br>Recursos de detecção da central de segurança do Azure](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Use os [recursos de detecção](../../security-center/security-center-alerts-overview.md#detect-threats)para identificar as ameaças ativas visando seus recursos de Microsoft Azure.</li><li>Use a [inteligência de ameaças integrada](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) que procura atores conhecidos, aproveitando a inteligência contra ameaças globais dos produtos e serviços da Microsoft, da [DCU (unidade de crimes digitais)](https://www.microsoft.com/trustcenter/security/cybercrime)da Microsoft, do [MSRC (Microsoft Security Response Center) ](response-center.md)e feeds externos.</li><li>Use a [análise comportamental](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) que aplica padrões conhecidos para descobrir comportamentos mal-intencionados. </li><li>Use a [detecção](https://msdn.microsoft.com/library/azure/dn913096.aspx) de anomalias que usa a criação de perfil estatística para criar uma linha de base histórica.</li></ul> |
| [<br>Operações do desenvolvedor (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>A [Infraestrutura como código (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) é uma prática, que permite a automação e a validação da criação e da desmontagem de redes e máquinas virtuais para ajudar a fornecer plataformas de Hospedagem de aplicativos seguras e estáveis.</li><li>A [integração e a implantação contínua](https://www.visualstudio.com/docs/build/overview) impulsionam a mesclagem contínua e o teste de código, o que leva a encontrar defeitos antecipadamente. </li><li>[Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) Gerencie implantações automatizadas em cada estágio do seu pipeline.</li><li>[Monitoramento de desempenho do aplicativo](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) de aplicativos em execução, incluindo ambientes de produção para integridade do aplicativo e uso do cliente, ajudam as organizações a formar uma hipótese e a validar rapidamente ou refutar estratégias.</li><li>Usando [testes de carga & dimensionamento automático](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) , podemos encontrar problemas de desempenho em nosso aplicativo para melhorar a qualidade da implantação e garantir que nosso aplicativo esteja sempre ativo ou disponível para atender às necessidades dos negócios.</li></ul> |


## <a name="conclusion"></a>Conclusão
Muitas organizações implantaram e operaram com êxito seus aplicativos na nuvem no Azure. As listas de verificação fornecidas destacam várias listas de verificação que são essenciais e ajudam você a aumentar a probabilidade de implantações bem-sucedidas e operações sem frustração. É altamente recomendável que essas considerações operacionais e estratégicas para suas implantações de aplicativo novas e existentes no Azure.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre segurança, consulte os seguintes artigos:

- [Design e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Planejamento e operações da central de segurança do Azure](../../security-center/security-center-planning-and-operations-guide.md).
