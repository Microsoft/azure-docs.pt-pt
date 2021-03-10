---
title: As contas de serviço no local | Diretório Ativo Azure
description: Um guia para criar e executar um processo de ciclo de vida de conta para contas de serviço
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557374"
---
# <a name="governing-on-premises-service-accounts"></a>Que regem as contas de serviço no local

Existem quatro tipos de contas de serviço no Windows Ative:

* [Contas de serviço geridas pelo grupo](service-accounts-group-managed.md) (gMSAs)

* [contas de serviço geridas autónomas](service-accounts-standalone-managed.md) (SMSAs)

* [Contas de computador](service-accounts-computer.md)

* [Contas de utilizador funcionando como contas de serviço](service-accounts-user-on-premises.md)


É fundamental governar de perto as contas de serviços para: 

* Proteja as contas de serviço com base nos seus requisitos e finalidades.

* Gerir o ciclo de vida das contas de serviço e as suas credenciais.

* Avaliar as contas de serviço com base no risco a que serão expostos e nas permissões que carregam, 

* Certifique-se de que o Ative Directory e o Azure Ative Directory não têm contas de serviços com permissões potencialmente abrangentes.

## <a name="principles-for-creating-a-new-service-account"></a>Princípios para a criação de uma nova conta de serviço

Utilize os seguintes critérios ao criar uma nova conta de serviço.

| Princípios| Considerações | 
| - |- | 
| Mapeamento de conta de serviço| Ligue a conta de serviço a um único serviço, aplicação ou script. |
| Propriedade| Certifique-se de que há um proprietário que solicita e assume a responsabilidade pela conta. |
| Âmbito| Defina o âmbito de aplicação claramente e antecipe a duração de utilização da conta de serviço. |
| Objetivo| Crie contas de serviço para um único propósito específico. |
| Privilege| Aplicar o princípio do menor privilégio por: <br>Nunca os atribuímos a grupos integrados como administradores.<br> Remover os privilégios da máquina local, se for caso disso.<br>Personalizar o acesso e utilizar a delegação do Ative Directory para acesso ao diretório.<br>Usando permissões de acesso granular.<br>Definição de expirações de conta e restrições baseadas na localização nas contas de serviço baseadas no utilizador |
| Utilização de monitorização e auditoria| Monitorize os dados de inscrição e certifique-se de que corresponde à utilização pretendida. Desacorra alertas para uma utilização anómala. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Impor o menor privilégio para contas de utilizador e limitar o uso excessivo da conta

Utilize as seguintes definições com contas de utilizador utilizadas como contas de serviço:

* [**Expiração da conta**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): desempura a conta de serviço para expirar automaticamente um tempo definido após o período de revisão, a menos que seja determinado que deve continuar

*  **Início de sposição:** restringir permissões para onde a conta de serviço pode iniciar sposição. Se funciona localmente numa máquina e acede apenas a recursos nessa máquina, impeça-a de iniciar sessão em qualquer outro lugar.

* [**Não é possível alterar a palavra-passe**](/powershell/module/addsadministration/set-aduser): impedir que a conta de serviço altere a sua própria palavra-passe, definindo o parâmetro para falso.

 
## <a name="build-a-lifecycle-management-process"></a>Construir um processo de gestão do ciclo de vida

Para manter a segurança das suas contas de serviço, tem de as gerir a partir do momento em que identificar a necessidade até que sejam desativadas. 

Utilize o seguinte processo de gestão do ciclo de vida das contas de serviço:

1. Recolher informações de utilização para a conta
1. A bordo da conta de serviço e da app para a base de dados de gestão de configuração (CMDB)
1. Realizar avaliação de risco ou revisão formal
1. Crie a conta de serviço e aplique restrições.
1. Agende e execute avaliações recorrentes. Ajuste as permissões e os âmbitos conforme necessário.
1. Deprovision conta quando apropriado.

### <a name="collect-usage-information-for-the-service-account"></a>Recolher informações de utilização para a conta de serviço

Recolher as informações de negócio relevantes para cada conta de serviço. A tabela abaixo mostra informações mínimas a serem recolhidas, mas deve recolher tudo o que for necessário para que o negócio seja o caso da existência das contas.

| Dados| Detalhes |
| - | - |
| Proprietário| Utilizador ou grupo que é responsável pela conta de serviço |
| Objetivo| Finalidade da conta de serviço |
| Permissões (Âmbitos)| Conjunto esperado de permissões |
| Ligações de base de dados de gestão de configuração (CMDB)| Conta de serviço cross-link com script/aplicação-alvo e proprietário(s) |
| Risco| Pontuação do impacto do risco e do negócio com base na avaliação do risco de segurança |
| Vida útil| Prazo máximo previsto para permitir o agendamento da caducidade ou recertificação da conta |


 

Idealmente, faça o pedido de um self-service de conta, e requer a informação relevante. O proprietário, que pode ser um proprietário de aplicação ou empresário, um membro de TI ou um proprietário de infraestrutura. A utilização de uma ferramenta como os formulários da Microsoft para este pedido e as informações associadas facilitarão a sua entrada na sua ferramenta de inventário CMDB se a conta for aprovada.

### <a name="onboard-service-account-to-cmdb"></a>Conta de serviço a bordo do CMDB

Armazenar as informações recolhidas numa aplicação do tipo CMDB. Além da informação empresarial, incluem todas as dependências de outras infraestruturas, apps e processos.  Este repositório central facilitará:

* Avaliar o risco.

* Configure a conta de serviço com as restrições exigidas.

* Compreender as dependências funcionais e de segurança relevantes.

* Realizar revisões regulares para segurança e necessidades contínuas.

* Contacte o(s) proprietário(s) para revisão, aposentação e alteração da conta de serviço.

Considere uma conta de serviço que é usada para executar um web site e tem privilégios para se conectar a uma ou mais bases de dados SQL. As informações armazenadas no seu CMDB para esta conta de serviço podem ser:

|Dados | Detalhes|
| - | - |
| Proprietário, Adjunto| John Bloom |
| Objetivo| Execute a página de RH e ligue-se às bases de dados de RH. Pode personificar o utilizador final ao aceder a bases de dados. |
| Permissões, Âmbitos| HR-WEBServer: faça login localmente, executar página web<br>HR-SQL1: faça login localmente, Leia em todas as bases de dados HR*<br>HR-SQL2: faça login localmente, LEIA na base de dados SALARY* |
| Centro de Custos| 883944 |
| Risco Avaliado| Médio; Impacto do Negócio: Médio; informação privada; Média |
| Restrições de conta| Iniciar sessão em: apenas servidores acima mencionados; Não é possível alterar a palavra-passe; Política MBI-Password; |
| Vida útil| sem restrições |
| Ciclo de revisão| Bianualmente (por proprietário, por equipa de segurança, por privacidade) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Efetuar avaliação de risco ou revisão formal da utilização da conta de serviço

Dadas as suas permissões e finalidade, avalie o risco que a conta pode representar para a sua aplicação ou serviço associado e para a sua infraestrutura se estiver comprometida. Considere o risco direto e indireto. 

* A que um adversário teria acesso direto?

* Que outras informações ou sistemas podem aceder à conta de serviço?

* A conta pode ser usada para conceder permissões adicionais?

* Como saberá quando as permissões mudarem?

A avaliação dos riscos, uma vez realizada e documentada, pode ter impacto em:

* Restrições de conta

* Vida útil da conta

* Requisitos de revisão de conta (cadência e revisores)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Criar uma conta de serviço e aplicar restrições de conta

Crie conta de serviço apenas depois de as informações relevantes estiverem documentadas no seu CMDB e realizar uma avaliação de risco. As restrições à conta devem ser alinhadas com a avaliação de risco. Considere as seguintes restrições quando relevante para a sua avaliação.:

* [Expiração da conta](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * Para todas as contas de utilizador utilizadas como contas de serviço, defina uma data final realista e definitiva para utilização. Desa esta utilização da bandeira "Sucedes da Conta". Para obter mais detalhes, consulte a[ Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Iniciar sessão[(LogonWorkstation)](/powershell/module/addsadministration/set-aduser)

* Requisitos [de Política de Passwords](../../active-directory-domain-services/password-policy.md)

* Criação num [local da UO](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) que garante a gestão apenas para utilizadores privilegiados

* Configurar e recolher [auditorias que detetem alterações](/windows/security/threat-protection/auditing/audit-directory-service-changes) na conta de serviço – e [utilização da conta de serviço](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Quando estiver pronto para ser posto em produção, conceda o acesso à conta de serviço de forma segura. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Agendar revisões regulares das contas de serviço

Configurar revisões regulares das contas de serviço classificadas como de risco médio e elevado. Os comentários devem incluir: 

* Atestado do proprietário para a necessidade contínua da conta, e justificação de privilégios e âmbitos.

* Revisão por equipas de privacidade e segurança, incluindo a avaliação das ligações a montante e a jusante.

* Dados de auditorias que garantam a sua utilização apenas para as finalidades a que se destinam

### <a name="deprovision-service-accounts"></a>Contas de serviço de desprovisionamento

No seu processo de desprovisionamento, primeiro remova permissões e monitorize e, em seguida, remova a conta se for caso disso.

Deprovision contas de serviço quando:

* O script ou aplicação para o qual a conta de serviço foi criada está aposentado.

* A função dentro do script ou aplicação, para a qual a conta de serviço é utilizada (por exemplo, acesso a um recurso específico) é reformada.

* A conta de serviço foi substituída por uma conta de serviço diferente.

Depois de remover todas as permissões, utilize este processo para remover a conta.

1. Uma vez desprovisionada a aplicação ou script associado, monitorize as entradas e o acesso aos recursos para a(s) conta de serviço associada para ter certeza de que não é utilizado em outro processo. Se tem certeza de que já não é necessário, vá para o próximo passo.

2. Desative a conta de serviço da inscrição e certifique-se de que já não é necessária. Criar uma política de negócios para as contas de tempo deve permanecer desativada.

3. Eliminar a conta de serviço após o cumprimento da política de desativação. 

   * Para os MSAs, pode [desinstalá-lo](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) utilizando o PowerShell ou eliminar manualmente do recipiente de conta de serviço gerido.

   * Para contas de computador ou utilizador, pode eliminar manualmente a conta no Ative Directory.

## <a name="next-steps"></a>Passos seguintes
Ver os seguintes artigos sobre a garantia de contas de serviço

* [Introdução às contas de serviço no local](service-accounts-on-premises.md)

* [Contas de serviço geridas pelo grupo seguro](service-accounts-group-managed.md)

* [Contas de serviço geridas autónomas seguras](service-accounts-standalone-managed.md)

* [Contas de computador seguras](service-accounts-computer.md)

* [Proteger contas de utilizador](service-accounts-user-on-premises.md)

* [Governar contas de serviço no local](service-accounts-govern-on-premises.md)