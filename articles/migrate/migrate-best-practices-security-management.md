---
title: Melhores práticas para proteger e gerenciar as cargas de trabalho migradas para o Azure | Documentos da Microsoft
description: Depois de migrar para o Azure, obtenha as práticas recomendadas para operar, gerir e proteger as cargas de trabalho migradas.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: 0bd19492c844e217dc520ae0c189ca467bb7ac0a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011096"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Melhores práticas para proteger e gerenciar as cargas de trabalho migradas para o Azure

Como planear e conceber para a migração, além de pensar sobre a migração em si, precisa considerar o seu modelo de segurança e gestão no Azure após a migração. Este artigo descreve o planeamento e práticas recomendadas para proteger a sua implementação do Azure após a migração e para as tarefas em curso manter a sua implementação em execução num nível de ideal. 

> [!IMPORTANT]
> As melhores práticas e opiniões descritos neste artigo baseiam-se na plataforma do Azure e funcionalidades disponíveis no momento da escrita de serviço. Funcionalidades e capacidades mudam ao longo do tempo.

## <a name="secure-migrated-workloads"></a>Proteger cargas de trabalho migradas

Após a migração, a tarefa mais importante é proteger cargas de trabalho migradas de ameaças internas e externas. Essas práticas recomendadas ajudá-lo a fazer isso:

- [Trabalhar com o Centro de segurança do Azure](#best-practice-follow-azure-security-center-recommendations): Saiba como trabalhar com a monitorização, avaliações e as recomendações fornecidas pelo centro de segurança do Azure
- [Criptografar seus dados](#best-practice-encrypt-data): Obtenha as práticas recomendadas para encriptar os dados no Azure.
- [Configurar o antimalware](#best-practice-protect-vms-with-antimalware): Proteja as suas VMs contra malware e ataques maliciosos.
- [Proteger aplicações web](#best-practice-secure-web-apps): Proteger informações confidenciais em aplicativos da web migrados.
- [Consultar subscrições](#best-practice-review-subscriptions-and-resource-permissions): Certifique-se de que pode aceder às suas subscrições do Azure e os recursos após a migração.
- [Trabalhar com registos](#best-practice-review-audit-and-security-logs): Reveja os registos de auditoria e segurança do Azure em intervalos regulares.
- [Reveja a outros recursos de segurança](#best-practice-evaluate-other-security-features): Compreender e avaliar as funcionalidades de segurança avançada que o Azure oferece.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Prática recomendada: Recomendações do Centro de segurança do Azure siga

A Microsoft trabalha duro para garantir que os administradores de inquilinos do Azure têm as informações necessárias para ativar funcionalidades de segurança que protegerem cargas de trabalho de ataques.  Centro de segurança do Azure oferece gestão de segurança unificada. O Centro de segurança, pode aplicar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detetar e responder a ataques. Centro de segurança analisa os recursos e configurações em inquilinos do Azure e faz recomendações de segurança, incluindo:

- **Gestão da política centralizada** – Certifique-se de que cumpre os requisitos de segurança regulamentar ou da empresa ao gerir centralmente políticas de segurança em todas as suas cargas de trabalho da cloud híbridas.
- **Avaliação de segurança contínua** – monitorize a postura de segurança das máquinas, das redes, do armazenamento e dos serviços de dados e aplicações para detetar potenciais problemas de segurança.
- **Recomendações acionáveis** – corrija vulnerabilidades de segurança antes de serem exploradas pelos atacantes com recomendações de segurança prioritárias e acionáveis.
- **Alertas e incidentes priorizados** - concentre-se primeiro nas ameaças mais importantes com alertas de segurança e de incidentes definidos.

Além de avaliações e recomendações, o Centro de segurança oferece uma série de outros recursos de segurança que podem ser ativadas para recursos específicos.

- **Acesso de apenas no Time (JIT)**: Reduza a superfície de ataque de rede com just-in-time, acesso controlado às portas de gestão em VMs do Azure.
    - Facto da porta de VM RDP 3389 aberta na internet expõe as VMs para atividades contínuas de má de ator. Endereços IP do Azure são bem conhecidos e hackers os sonda continuamente para ataques em abrir 3389 portas. 
    - Just-in da segurança de rede utiliza grupos (NSGs) e a entrada de regras desse limite a quantidade de tempo que uma porta específica está aberta.
    - Com apenas no momento em que ativado, o Centro de segurança verifica se um utilizador tem permissões de acesso de escrita do acesso baseado em funções (RBAC) do controle para uma VM. Além disso, especifica regras para como os utilizadores podem ligar a VMs. Se as permissões estão OK, um pedido de acesso é aprovado e Centro de segurança configura NSGs para permitir o tráfego de entrada para portas selecionadas para a quantidade de tempo que especificar. NSGs são volte ao estado anterior dos quando o período expira.
- **Controlos de aplicação adaptável**: Continuar a software e malware desligar as VMs ao controlar quais aplicativos são executados nos mesmos utilizar listas de permissões de aplicação dinâmica.
    - Controlos de aplicação adaptável permitem-lhe a lista de permissões de aplicações e impedem que usuários não autorizados ou administradores instalar aplicações de software não aprovadas ou aprovação nas suas VMs.
    - Pode bloquear ou alerta tenta executar aplicações maliciosas, evitar aplicativos indesejados ou maliciosos e garantir a conformidade com a política de segurança de aplicações da sua organização.
- **Monitorização da integridade de ficheiros**: Certifique-se a integridade dos ficheiros em execução em VMs.
    - Não precisa de instalar o software para fazer com que os problemas VM.  Alterar um arquivo de sistema também pode causar degradação de falha ou o desempenho da VM.  Monitorização examina os arquivos de sistema e as definições de registo de alterações e notifica se algo é atualizado de integridade de ficheiros.
    - Recomenda do Centro de segurança que os ficheiros deve monitorizar.


**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/security-center/security-center-intro) sobre o Centro de segurança do Azure.
- [Saiba mais](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) sobre just-in-acesso à VM do tempo.
- [Saiba mais sobre](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) aplicar controlos de aplicação adaptável.
- [Introdução ao](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) com a monitorização da integridade de ficheiros.


## <a name="best-practice-encrypt-data"></a>Prática recomendada: Encriptar dados 

A encriptação é uma parte importante de práticas de segurança do Azure. Garantir que a encriptação está ativada em todos os níveis de ajuda a impedir que partes não autorizadas de obter acesso a dados confidenciais, incluindo os dados em trânsito e em inatividade. 

### <a name="encryption-for-iaas"></a>Encriptação para IaaS

- **VMs**: Para VMs pode utilizar o Azure Disk Encryption para encriptar os discos da VM de IaaS de Linux e Windows.
    - Encriptação de disco tira partido do BitLocker para Windows e o DM-Crypt para Linux para fornecer encriptação de volume para os discos de SO e dados.
    - Pode utilizar uma chave de encriptação criada pelo Azure, ou pode fornecer as suas próprias chaves de encriptação, guardados de forma segura no Azure Key Vault. 
    - Com a encriptação de disco, os dados da VM de IaaS são protegidos na rest (no disco) e durante o arranque da VM. 
    - Centro de segurança do Azure alerta-o se tiver VMs que não estão encriptadas.
- **Armazenamento**: Protege os dados rest armazenados no armazenamento do Azure.
    - Dados armazenados em contas de armazenamento do Azure podem ser encriptados utilizando chaves AES gerados pelo Microsoft que têm a certificação FIPS 140-2 em conformidade ou pode usar suas próprias chaves.
    - Encriptação do serviço de armazenamento está ativada para todas as contas de armazenamento de novas e existentes e não pode ser desativada.


### <a name="encryption-for-paas"></a>Encriptação para PaaS

Ao contrário de onde a gerenciar suas próprias VMs e a infraestrutura de IaaS, numa PaaS plataforma de modelo e a infraestrutura é gerida pelo provedor, permitindo a concentrar-se na lógica de aplicação do principal e capacidades. Com tantos diferentes tipos de serviços de PaaS, cada serviço será avaliado individualmente por motivos de segurança. Por exemplo, vamos ver como podemos poderá ativar a encriptação da base de dados do Azure SQL.

- **Sempre encriptado**: Utilize o assistente encriptado sempre no SQL Server Management Studio para proteger dados em repouso.
    - Criar chave Always Encrypted para encriptar dados da coluna individuais.
    - Sempre encriptado chaves podem ser armazenadas como encriptadas nos metadados da base de dados ou armazenadas nos arquivos de chave fidedignos, como o Azure Key Vault.
    - Alterações da aplicação provavelmente serão necessário para utilizar esta funcionalidade.
- **Encriptação de dados transparente (TDE)**: Protege a base de dados do SQL do Azure com a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso.
    - TDE permite que as atividades de encriptação ter lugar sem alterações ao nível da aplicação.
    - TDE pode utilizar chaves de encriptação fornecidas pela Microsoft ou pode fornecer suas próprias chaves a utilizar o suporte de traga a sua própria chave.


**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) Azure Disk Encryption para VMs de IaaS.
- [Ativar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) encriptação para VMs do Windows de IaaS.
- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) encriptação do serviço de armazenamento do Azure para dados inativos.
- [Leitura](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) uma visão geral de Always Encrypted.
- [Leia sobre](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) TDE para a base de dados SQL do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) TDE com traga a sua própria chave.

## <a name="best-practice-protect-vms-with-antimalware"></a>Prática recomendada: Proteger VMs com antimalware

Em particular, VMs migradas do Azure anteriores podem não ter o nível apropriado de antimalware instalado.  O Azure fornece uma solução de ponto de extremidade gratuito que ajuda a proteger VMs contra vírus, spyware e outro software maligno.
- O Microsoft Antimalware para o Azure gera alertas quando conhecido software malicioso ou indesejável se tentar instalar-se.
- É uma solução de único agente que é executado em segundo plano sem intervenção humana.
- No Centro de segurança do Azure, é possível identificar facilmente as VMs que não têm a proteção de ponto final em execução e instalar o Microsoft Antimalware conforme necessário.

![Antimalware para as VMs](./media/migrate-best-practices-security-management/antimalware.png)
*Antimalware para as VMs*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/security/azure-security-antimalware) Antimalware da Microsoft.

## <a name="best-practice-secure-web-apps"></a>Prática recomendada: Proteger aplicações web

Aplicações web migrados enfrentam alguns problemas:

- A maioria dos aplicativos web herdados tendem a ter informações confidenciais dentro de arquivos de configuração.  Ficheiros que contêm essas informações podem apresentar problemas de segurança, quando as aplicações são uma cópia de segurança, ou quando o código de aplicação é verificado dentro ou fora do controlo de origem.
- Além disso, quando migrar aplicações web que reside numa VM, se é provavelmente a mover essa máquina de um ambiente protegido por firewall de rede no local e a um ambiente com acesso à internet. Tem de certificar-se de que defina uma solução que funciona da mesma forma como o ambiente de recursos de proteção.


O Azure fornece duas soluções:

- **Cofre de chaves do Azure**: Hoje os desenvolvedores de aplicativos web são passos para garantir que informações confidenciais não estão a fuga destes ficheiros. Métodos para proteger as informações é extraí-lo a partir dos ficheiros e colocá-la num Azure Key Vault.
    - Pode utilizar o Key Vault para centralizar o armazenamento dos segredos da aplicação e controlar a distribuição dos mesmos. Evita a necessidade de armazenar informações de segurança em ficheiros de aplicação.
    - Aplicações podem obter informações de acesso de segurança no cofre utilizando URIs, sem a necessidade de código personalizado.
    - O Azure Key Vault permite-lhe bloquear o acesso através de controlos de segurança do Azure e a implementação direta de "chaves sem interrupção". Microsoft não veja ou extraia os dados.
- **Ambiente de serviço de aplicações**: Se precisar de uma aplicação que migrar proteção extra, pode considerar a inclusão de um ambiente de serviço de aplicações e uma Firewall de aplicações Web para proteger os recursos de aplicação.
    - O ambiente de serviço de aplicações do Azure fornece um ambiente totalmente isolado e dedicado em que a executar o serviço de aplicações, aplicações, tais como o Windows e aplicações web do Linux, contentores Docker, aplicações móveis e as funções.
    - É útil para aplicações que são muito grande escala, que requerem isolamento e de rede segura de acesso ou tem a utilização de memória elevada
- **Firewall de aplicações Web**: Um recurso do Gateway de aplicação que fornece proteção centralizada para aplicações web.
    - Ele protege aplicações web sem a necessidade de modificações no código de back-end.
    - Ele protege várias aplicações web em simultâneo por trás de um gateway de aplicação.
    - Firewall de aplicações Web pode ser monitorizada com o Azure Monitor e está integrado no Centro de segurança do Azure.



![Proteger aplicações web](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/key-vault/key-vault-overview) do Cofre de chaves do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/application-gateway/waf-overview) firewall de aplicações Web.
- [Obtenha uma introdução](https://docs.microsoft.com/azure/app-service/environment/intro) para ambientes de serviço de aplicações.
- [Saiba como](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) configurar uma aplicação web para ler segredos do Key Vault.
- [Saiba mais sobre](https://docs.microsoft.com/azure/application-gateway/waf-overview) Firewall de aplicações Web

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Prática recomendada: Subscrições de revisão e permissões de recursos

Como migrar as cargas de trabalho e execute-os no Azure, a equipe com acesso de carga de trabalho move-se. A equipa de segurança deve rever o acesso aos seus grupos de recursos e de inquilino do Azure em intervalos regulares. O Azure fornece um número de ofertas de gerenciamento de identidade e segurança de controlo de acesso, incluindo o controlo de acesso baseado em funções (RBAC) para autorizar permissões para aceder aos recursos do Azure.

- RBAC atribui as permissões de acesso para principais de segurança. Entidades de segurança representam os utilizadores, grupos (um conjunto de utilizadores), (utilizada por aplicações e serviços de identidade) de principais de serviço e gerido identidades (uma identidade do Azure Active Directory automaticamente gerida pelo Azure).
- RBAC pode atribuir funções de princípios de segurança, como o proprietário, Contribuidor e leitor e função definições (uma coleção de permissões) que definem as operações que podem ser executadas pelas funções.
- RBAC também pode definir âmbitos de definir o limite para uma função. Âmbito pode ser definido num número de níveis, incluindo um grupo de gestão, subscrição, grupo de recursos ou recurso
- Certifique-se de que os administradores com acesso do Azure só são capazes de aceder a recursos que deseja permitir.  Se as funções predefinidas no Azure não são suficientemente granulares, pode criar funções personalizadas para separar e limitar as permissões de acesso.

![Controlo de acesso](./media/migrate-best-practices-security-management/subscription.png)
*controlo de acesso - IAM*

**Saiba mais:**

- [Sobre](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Saiba](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) para gerir o acesso com RBAC e o portal do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) funções personalizadas.

## <a name="best-practice-review-audit-and-security-logs"></a>Prática recomendada: Rever os registos de auditoria e segurança

O Azure Active Directory (AD) fornece os registos de atividades que aparecem no Azure Monitor. Os registos de capturam as operações executadas em inquilinos do Azure, onde ocorreram e quem realizou-los. 

- Registos de auditoria mostram o histórico de tarefas no inquilino. Mostrar quem levadas a cabo as tarefas de registos de atividades de início de sessão. 
- Acesso a relatórios de segurança depende da sua licença do Azure AD. No gratuito e básico, obter uma lista de utilizadores de risco e inícios de sessão. Nas edições Premium 1 e Premium 2 obter subjacente informações do evento.
- Pode encaminhar os registos de atividades para um número de pontos finais para retenção a longo prazo e informações de dados.
- Torna-se de uma prática comum para rever os registos ou integrar as ferramentas de gestão (SIEM) de segurança informações e eventos, para rever automaticamente anomalias.  Se não estiver a utilizar o Premium 1 ou 2, terá de fazer um grau de análise, mesmo ou utilizar o seu sistema SIEM.  A análise inclui à procura de inícios de sessão de risco e eventos e outros padrões de ataque do utilizador.


![Utilizadores e grupos](./media/migrate-best-practices-security-management/azure-ad.png)
*grupos e utilizadores do Azure AD*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) registos de atividades do Azure AD no Azure Monitor.
- [Saiba como](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) atividade relatórios de auditoria no portal do Azure AD.

## <a name="best-practice-evaluate-other-security-features"></a>Prática recomendada: Avalie outros recursos de segurança

O Azure fornece uma série de outros recursos de segurança que fornecem opções de segurança avançada. Algumas dessas melhores práticas necessitam de licenças de suplemento e as opções de premium.

- **Implementar unidades administrativas do Azure AD (AU)**: Delegar tarefas administrativas para suporte técnico pode ser complicado com controlo de acesso do Azure apenas básica.  Fornecer acesso de equipe de suporte para administrar todos os grupos no Azure AD pode não ser a abordagem ideal para segurança organizacional.  Usar o AU permite-lhe separar os recursos do Azure para contentores de forma semelhante às unidades organizacionais no local (UO).  Para utilizar o AU o administrador de AU tem de ter uma licença do Azure AD premium. [Saiba mais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Utilizar a autenticação multifator (MFA)**: Se tiver uma licença do Azure AD premium pode ativar e impor a MFA no seu contas de administrador. Phishing é a forma mais comum que as credenciais de contas sejam comprometidas.  Depois que um ator indevido tiver credenciais de conta de administrador, há como pará-los de ações de longo alcance, como a eliminação de todos os seus grupos de recursos. Pode configurar MFA de diversas formas, incluindo com o e-mail, a aplicação de autenticador e mensagens de texto do telefone. Como um administrador pode selecionar a opção menos intrusiva. MFA integra-se com a análise de ameaças e respondem de políticas de acesso condicional para exigir aleatoriamente um desafio MFA. Saiba mais sobre [orientações de segurança](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices), e [como configurar a](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA.
- **Implementar o acesso condicional**: Na maioria das organizações de pequeno e médio tamanho, os administradores do Azure e a equipa de suporte provavelmente estão localizados numa única geografia. Neste caso, a maioria dos inícios de sessão serão provenientes das mesmo áreas. Se os endereços IP destas localizações estão razoavelmente estáticos, faz sentido que não os deveriam ver logons de administrador de fora essas áreas. Mesmo num evento em que um ator indevido remoto compromete a credenciais de um administrador, pode implementar recursos de segurança, como o acesso condicional, combinada com a MFA para impedir o início de sessão de localizações remotas ou a partir de localizações falsificadas de endereços IP aleatórios. [Saiba mais](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) sobre o acesso condicional, e [rever as melhores práticas](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) para o acesso condicional no Azure AD.
- **Reveja as permissões de aplicação empresarial**: Ao longo do tempo, os administradores clique em Microsoft e ligações de terceiros sem saber o seu impacto na organização. Ligações podem apresentar os ecrãs de consentimento que atribuir permissões a aplicações do Azure e poderão dar acesso para ler dados do Azure AD, ou até mesmo total acesso para gerir a sua subscrição do Azure completa. Deve rever regularmente as aplicações ao qual os administradores e utilizadores têm permissão para aceder aos recursos do Azure. Deve garantir que estas aplicações têm apenas as permissões que são necessárias. Além disso, trimestralmente ou anualmente ponto e pode enviar um e-mail aos utilizadores com uma ligação para as páginas de aplicação para que elas estejam cientes das aplicações aos quais eles tiverem permissão para aceder aos dados organizacionais. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) sobre os tipos de aplicativo, e [como controlo](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) atribuições de aplicações no Azure AD.



## <a name="managed-migrated-workloads"></a>Cargas de trabalho migradas geridas

Nesta secção vamos recomendar algumas das melhores práticas para gestão do Azure, incluindo:

- [Gerir recursos](#best-practice-name-resource-groups): Melhores práticas para grupos de recursos do Azure e recursos, incluindo inteligentes de nomenclatura, impedir a eliminação acidental, gestão de permissões de recursos e a identificação de recursos em vigor.
- [Utilize planos gráficos](#best-practice-implement-blueprints): Obtenha uma visão geral sobre como utilizar planos gráficos para criar e gerir os seus ambientes de implantação.
- [Rever arquiteturas](#best-practice-review-azure-reference-architectures): Rever arquiteturas do Azure para aprender de à medida que cria as suas implementações de pós-migração de exemplo.
- [Configurar grupos de gestão](#best-practice-manage-resources-with-management-groups): Se tiver várias subscrições, pode reuni-los em grupos de gestão e aplicar definições de governação a esses grupos.
- [Configurar políticas de acesso](#best-practice-deploy-azure-policy): Aplica políticas de conformidade aos recursos do Azure.
- [Implementar uma estratégia BCDR](#best-practice-implement-a-bcdr-strategy): Criar uma estratégia de recuperação (BCDR) de desastre e continuidade comercial para acompanhar dados seguros, de seu ambiente resiliente e de recursos e em execução quando ocorrem falhas.
- [Gerir VMs](#best-practice-use-managed-disks-and-availability-sets): Agrupar VMs em grupos de disponibilidade para elevada disponibilidade e resiliência. Utilize discos geridos para facilidade de gerenciamento de disco e o armazenamento VM.
- [Monitorizar a utilização de recursos](#best-practice-monitor-resource-usage-and-performance): Ativar o registo de diagnóstico de recursos do Azure, criar alertas e playbooks para resolução de problemas proativa e utilizar o dashboard do Azure para uma vista unificada do seu estado de funcionamento da implementação e o estado.
- [Gerir atualizações e suporte](#best-practice-manage-updates): Compreenda o seu plano de suporte do Azure e como implementá-lo, obtenha as práticas recomendadas para manter atualizadas de VMs e os processos put no local para o gerenciamento de alterações.


## <a name="best-practice-name-resource-groups"></a>Prática recomendada: Grupos de recursos de nome

Garantir que seus grupos de recursos têm nomes significativos que os administradores e os membros da equipe de suporte fácil podem reconhecer e navegue drasticamente melhorará a produtividade e eficiência.
- É recomendável seguir as convenções de nomenclatura do Azure.
- Se estiver sincronizando o ambiente AD DS para o Azure AD com o AD Connect, considere a correspondência de nomes de segurança grupos no local para os nomes dos grupos de recursos no Azure.

![Nomenclatura](./media/migrate-best-practices-security-management/naming.png)
*nomenclatura do grupo de recursos*


**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) convenções de nomenclatura

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Prática recomendada: Implementar eliminar bloqueios para grupos de recursos

A última coisa que precisa destina-se um grupo de recursos desaparecer porque foi eliminado acidentalmente. Recomendamos que implementar delete bloqueios, para que isso não acontece.


![Eliminar bloqueios](./media/migrate-best-practices-security-management/locks.png)
*eliminar bloqueios*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) bloquear os recursos para prevenir alterações inesperadas.


## <a name="best-practice-understand-resource-access-permissions"></a>Prática recomendada: Compreender as permissões de acesso de recursos 

Um proprietário da subscrição tem acesso a todos os grupos de recursos e recursos na sua subscrição.
- Adicione as pessoas com moderação para esta atribuição valiosa. Compreender as ramificações desses tipos de permissões é importante manter o seu ambiente seguro e estável.
- Certifique-se de que coloque recursos em grupos de recursos adequados:
    - Corresponde a recursos com um ciclo de vida semelhante em conjunto. O ideal é que não deve precisar de mover um recurso quando precisa eliminar um grupo de recursos inteiro.
    - Recursos que oferecem suporte a uma função ou a carga de trabalho devem ser colocados em conjunto para gerenciamento simplificado.

**Saiba mais:**

- [Saiba mais sobre](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) organizar as subscrições e grupos de recursos.

## <a name="best-practice-tag-resources-effectively"></a>Prática recomendada: Etiquetar recursos com eficiência

Muitas vezes, usando apenas um nome do grupo de recursos relacionado aos recursos não fornecer suficiente metadados para a implementação efetiva de mecanismos como interno de faturação ou gestão dentro de uma subscrição.
- Como melhor prática, deve utilizar etiquetas do Azure para adicionar metadados úteis que podem ser consultados e comunicados. 
- As etiquetas proporcionam uma forma de organizar logicamente os recursos com propriedades que definir.  As etiquetas podem ser aplicadas diretamente aos grupos de recursos ou recursos.
- As etiquetas podem ser aplicadas num grupo de recursos ou em recursos individuais. Os sinalizadores de grupo de recursos não são herdados pelos recursos no grupo.
- Pode automatizar com o Powershell ou a automatização do Azure, ou a grupos individuais de marca e recursos de marcação. -marcação abordagem ou um self-service.  Se tiver um pedido e alterar o sistema de gestão no local, em seguida, pode facilmente utilizar as informações no pedido para preencher as suas etiquetas de recursos específicos da empresa.


![Etiquetagem](./media/migrate-best-practices-security-management/tagging.png)
*etiquetagem*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) marcação e marque as limitações.
- [Revisão](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) exemplos do PowerShell e CLI para configurar a etiquetagem e para aplicar etiquetas de um grupo de recursos a seus recursos.
- [Leitura](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) marcação de melhores práticas do Azure.


## <a name="best-practice-implement-blueprints"></a>Prática recomendada: Implementar planos gráficos

Tal como esquema permite que arquitetos e engenheiros criar esboços parâmetros de design de um projeto, Azure Blueprints permite a centrais de grupos de TI e arquitetos de nuvem definir um conjunto passível de repetição dos recursos do Azure que implementa e em conformidade com padrões de uma organização, padrões e requisitos. Utilizar as equipes de desenvolvimento de planos gráficos do Azure pode rapidamente compilar e criar novos ambientes a que cumpram os requisitos de conformidade organizacional e que têm um conjunto de componentes internos, como o funcionamento em rede para acelerar o desenvolvimento e entrega.

- Utilize planos gráficos para orquestrar a implementação de grupos de recursos, modelos Azure Resource Manager e as atribuições de política e a função.
- Planos gráficos do Azure são armazenados no Azure Cosmos DB e distribuída globalmente. Os objetos do Blueprint são replicados para várias regiões do Azure. Replicação fornece acesso consistente ao esquema, independentemente da região ao qual um plano gráfico implementa recursos, elevada disponibilidade e baixa latência.

**Saiba mais:**

- [Leitura](https://docs.microsoft.com/azure/governance/blueprints/overview) sobre esquemas.
- [Revisão](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) um exemplo de esquema utilizado para acelerar a ia nos cuidados de saúde.

## <a name="best-practice-review-azure-reference-architectures"></a>Prática recomendada: Rever arquiteturas de referência do Azure

A criação de segura e escalonável e gerenciáveis cargas de trabalho no Azure podem ser assustador.  Com alterações contínuas, ele pode ser difícil manter-se diferentes funcionalidades para um ambiente ideal. Ter uma referência para aprender com pode ser útil ao projetar e migrar cargas de trabalho.  Azure e Azure parceiros criaram várias arquiteturas de referência de exemplo para vários tipos de ambientes. Estas amostras foram concebidas para fornecer idéias que pode aprender e ampliar. 

Arquiteturas de referência são dispostas por cenário. Contêm Recomendamos práticas e recomendações sobre gerenciamento, disponibilidade, escalabilidade e segurança.
O ambiente de serviço de aplicações do Azure fornece um ambiente totalmente isolado e dedicado para executar o serviço de aplicações, aplicações, incluindo o Windows e aplicações web do Linux, contentores Docker, aplicações móveis e as funções. Serviço de aplicações adiciona o poder do Azure à sua aplicação, com segurança, balanceamento de carga, dimensionamento automático e gestão automatizada. Também pode tirar partido das respetivas capacidades de DevOps, como a implementação contínua a partir do Azure DevOps e GitHub, gestão de pacotes, ambientes de domínio personalizado e certificados SSL de teste. Serviço de aplicações é útil para aplicações que necessitam de isolamento e um acesso de rede e os que utilizam elevadas quantidades de memória e outros recursos que precisam de dimensionar.
**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/) arquiteturas de referência do Azure.
- [Revisão](https://docs.microsoft.com/azure/architecture/example-scenario/) cenários de exemplo do Azure.

## <a name="best-practice-manage-resources-with-management-groups"></a>Prática recomendada: Gerir os recursos com grupos de gestão

Se a sua organização tiver várias subscrições, terá de gerir o acesso, políticas e conformidade para os mesmos. Os grupos de gestão do Azure fornecem um nível de âmbito acima das subscrições.

- Organizar subscrições em chamados grupos de gestão de contentores e aplicar governação condições à mesma.
- Todas as subscrições num grupo de gestão herdam automaticamente as condições de grupo de gestão.
- Grupos de gestão fornecem a gestão de nível empresarial em grande escala, não importa que tipo de subscrições tiver.
- Por exemplo, pode aplicar uma política de grupo de gestão que limita as regiões em que as VMs podem ser criadas. Em seguida, a política é aplicada a todos os grupos de gestão, subscrições e recursos sob esse grupo de gestão.
- Pode criar uma estrutura flexível de grupos de gestão e subscrições, para organizar os recursos para uma hierarquia para gestão de acesso e política unificada.

O diagrama seguinte mostra um exemplo de criação de uma hierarquia de governação com grupos de gestão.

![Grupos de gestão](./media/migrate-best-practices-security-management/management-groups.png)
*grupos de gestão*

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/governance/management-groups/index) sobre como organizar recursos em grupos de gestão.

## <a name="best-practice-deploy-azure-policy"></a>Prática recomendada: Implementar política do Azure

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas.

- Políticas impõem diferentes regras e efeitos aos recursos, para que esses recursos mantêm-se em conformidade com os seus padrões empresariais e contratos de nível de serviço.
- O Azure Policy avalia os recursos, analisando aqueles que não em conformidade com as suas políticas.
- Por exemplo, pode criar uma política que permite que apenas um tamanho SKU específico para as VMs no seu ambiente. Política do Azure irá avaliar esta definição quando criar e atualizar recursos e quando efetua a análise de recursos existentes. 
- O Azure fornece algumas políticas incorporadas que pode atribuir ou pode criar o seu próprio.


![O Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*política do Azure*

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/governance/policy/overview) da política do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) criar e gerir políticas para impor a conformidade.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Prática recomendada: Implementar uma estratégia BCDR

Planear a continuidade do negócio e recuperação após desastre (BCDR), é um exercício crítico que devem ser concluídas durante o planejamento para a migração para o Azure. Em termos legais, o seu contrato inclui uma cláusula de "força maior" Desculpe obrigações devido a uma força maior, como furacões ou sismos. No entanto, tem também obrigações em torno de uma capacidade para se certificar de que os serviços irão continuar a executar e recuperar quando necessário, assim que desastre strike. Sua capacidade de fazer isso pode criar ou estragar o futuro da sua empresa.

De forma ampla, tem de considerar sua estratégia BCDR:
- **Cópia de segurança de dados**: Como manter os dados protegidos para que pode recuperá-la facilmente se ocorrerem falhas.
- **Recuperação após desastre**: Como manter as suas aplicações resilientes e disponíveis se ocorrerem falhas. 

### <a name="azure-resiliency-features"></a>Funcionalidades de resiliência do Azure
A plataforma do Azure fornece uma série de recursos de resiliência.

- **Emparelhamento de região**: Azure pares de regiões para fornecer proteção regional dentro dos limites de residência de dados. Azure assegura um isolamento físico entre pares de região, dá prioridade a recuperação de uma região do par em caso de uma falha abrangente, implementa atualizações de sistema separadamente em cada região e permite funcionalidades, tais como o armazenamento com redundância geográfica do Azure para replicar no pares regionais.
- **As zonas de disponibilidade**: As zonas de disponibilidade proteção contra falhas de todo o datacenter do Azure através do estabelecimento de zonas separadas físicas com uma região do Azure. Cada zona tem uma fonte de alimentação distintas, a infraestrutura de rede e o mecanismo de resfriamento.
- **Conjuntos de disponibilidade**: Conjuntos de disponibilidade se proteger contra falhas dentro de um datacenter. Agrupar VMs em conjuntos de disponibilidade para mantê-los altamente disponíveis. Dentro de cada conjunto de disponibilidade, o Azure implementa vários domínios de falha desse grupo em conjunto de hardware com uma fonte de energia comum e o comutador de rede e domínios de atualização que agrupar hardware subjacente que pode entrar em manutenção ou ser reiniciado, paralelos ao mesmo tempo. Por exemplo, quando uma carga de trabalho é distribuída em VMs do Azure, pode colocar duas ou mais VMs para cada camada de aplicação num conjunto. Por exemplo, pode colocar as VMs de front-end num conjunto e VMs de camada de dados em outro. Uma vez que apenas um domínio de atualização é cada reiniciado de cada vez num conjunto, e o Azure garante que as VMs num conjunto são distribuídas por domínios de falha, certifique-se de que nem todas as VMs num conjunto de falhará ao mesmo tempo.

### <a name="set-up-bcdr"></a>Configurar a BCDR

Ao migrar para o Azure, é importante compreender que embora a plataforma do Azure oferece estas capacidades de resiliência incorporadas, precisa para estruturar a implementação do Azure para tirar partido das funcionalidades do Azure e serviços que fornecem elevada disponibilidade, recuperação após desastre e cópia de segurança.

- Sua solução BCDR depende os objetivos da empresa e influenciado por sua estratégia de implementação do Azure. Infraestrutura como serviço (IaaS) e plataforma como um implementações de serviço (PaaS) apresentam desafios diferentes para BCDR.
- Uma vez no local, suas soluções BCDR devem ser testadas regularmente para verificar que a sua estratégia de permanece utilizável.


## <a name="best-practice-back-up-your-data"></a>Prática recomendada: Criar cópias de segurança dos dados

Na maioria dos casos, uma carga de trabalho no local foi descontinuada após a migração, e sua estratégia de no local para fazer backup de dados tem de ser estendida ou substituída. Se migrar todo o seu datacenter para o Azure, terá de criar e implementar uma solução de cópia de segurança completa através de tecnologias do Azure ou de terceiros de soluções integradas. 


### <a name="back-up-an-iaas-deployment"></a>Fazer cópias de segurança de uma implementação de IaaS

Para cargas de trabalho em execução em VMs de IaaS do Azure, considere estas soluções de cópia de segurança:

- **O Azure Backup**: Fornece cópias de segurança consistentes com aplicações para Windows Azure e VMs do Linux.
- **Instantâneos de armazenamento**: Criar instantâneos de armazenamento de Blobs.

#### <a name="azure-backup"></a>Azure Backup

Faz de cópia de segurança do Azure Backup cria pontos de recuperação de dados que estão armazenados no armazenamento do Azure. O Azure Backup pode criar cópias de segurança discos de VM do Azure e ficheiros do Azure (pré-visualização). Os ficheiros do Azure oferecem partilhas de ficheiros na cloud que são acessíveis através de SMB.
   
Pode utilizar o Azure Backup para fazer uma cópia de segurança de VMs de duas formas.

- **Cópia de segurança direta das definições de VM**: Pode fazer backup de VMs com cópia de segurança do Azure diretamente a partir das opções de VM no portal do Azure. Pode criar cópias de segurança da VM uma vez e o dia e restaurar o disco da VM, conforme necessário. O Azure Backup tira instantâneos de dados com suporte para aplicações (VSS), nenhum agente é instalado na VM.
- **Cópia de segurança direta num cofre dos serviços de recuperação**: Pode fazer backup de suas VMs do IaaS ao implementar um cofre dos serviços de recuperação de cópia de segurança do Azure. Isto fornece uma localização única para controlar e gerir cópias de segurança e fornece opções de cópia de segurança e restauro granulares. Cópia de segurança é até três vezes por dia, ao nível do ficheiro/pasta. Ela não conhece a aplicação e o Linux não é suportado. Tem de instalar o agente dos serviços de recuperação do Azure (MARS) da Microsoft em cada VM que pretende criar cópias de segurança.
- **Servidor de cópia de segurança do Azure: Proteger a VM para o Azure Backup Server**: O servidor de cópia de segurança do Azure é fornecido gratuitamente com o Azure Backup. A VM é uma cópia de segurança para o armazenamento local do servidor de cópia de segurança do Azure. , Em seguida, fazer uma cópia de segurança do servidor de cópia de segurança do Azure para o Azure num cofre. Cópia de segurança reconheça a aplicação, com uma granularidade completa através de cópia de segurança com frequência e retenção. Pode criar cópias de segurança ao nível da aplicação. Por exemplo, ao fazer backup do SQL Server ou SharePoint.

Para segurança, cópia de segurança do Azure encripta dados em trânsito com AES 256 e envia-os através de HTTPS para o Azure. Cópia de segurança de dados Inativos no Azure são encriptados utilizando [Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)e os dados de transmissão e o armazenamento.


![O Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*cópia de segurança do Azure*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) diferentes tipos de cópias de segurança.
- [Planear uma infraestrutura de cópia de segurança](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) para VMs do Azure.

#### <a name="storage-snapshots"></a>Instantâneos de armazenamento

VMs do Azure são armazenadas como blobs de páginas no armazenamento do Azure. 

- Instantâneos de capturam o estado do blob num ponto específico no tempo.
- Como um método de cópia de segurança alternativo para discos de VM do Azure, pode tirar um instantâneo de blobs de armazenamento e copiá-los para outra conta de armazenamento. 
- Pode copiar um blob de todo, ou utilizar um instantâneo incremental cópia para copiar apenas as alterações delta e reduzir o espaço de armazenamento.
- Como uma precaução extra, pode ativar a eliminação de forma recuperável para contas de armazenamento de Blobs. Com esta funcionalidade ativada, um blob que é eliminado é marcado para eliminação mas não imediatamente removido. Durante o período provisório, os BLOBs podem ser restauradas.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) armazenamento de Blobs do Azure.
- [Saiba como](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) criar um instantâneo de blob.
- [Reveja um cenário de exemplo](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) para cópia de segurança de armazenamento de Blobs.
- [Leia sobre](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) eliminação de forma recuperável.
- [Recuperação após desastre e ativação pós-falha forçada (pré-visualização) no armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>Cópia de segurança de terceiros

Além disso, pode utilizar as soluções de terceiros para fazer cópias de segurança de VMs do Azure e contentores de armazenamento para o armazenamento local ou outros fornecedores de cloud. [Saiba mais](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) sobre soluções de cópia de segurança no Azure marketplace. 


### <a name="back-up-a-paas-deployment"></a>Fazer cópias de segurança de uma implementação de PaaS


Ao contrário de onde a gerenciar suas próprias VMs e a infraestrutura de IaaS, numa PaaS plataforma de modelo e a infraestrutura é gerida pelo provedor, permitindo a concentrar-se na lógica de aplicação do principal e capacidades. Com tantos diferentes tipos de serviços de PaaS, cada serviço será avaliado individualmente para fins de cópia de segurança. Vamos analisar dois comuns serviços PaaS do Azure - base de dados do Azure SQL e as funções do Azure.

#### <a name="back-up-azure-sql-database"></a>Fazer cópias de segurança da base de dados do Azure SQL

Base de dados SQL do Azure é um motor de base de dados de PaaS totalmente gerido. Ele fornece uma série de funcionalidades de continuidade de negócio, incluindo automatizar cópia de segurança.

- Base de dados SQL efetua automaticamente cópias de segurança de base de dados completa semanal e cópias de segurança diferenciais a cada 12 horas. Backups de log de transação direcionados a cada cinco a dez minutos para proteger a base de dados contra a perda de dados.
- As cópias de segurança são transparentes e não a incorrer em custos adicionais.
- As cópias de segurança são armazenadas no armazenamento RA-GRS para redundância geográfica e replicadas para a região geográfica emparelhada.
- Retenção de cópia de segurança depende do modelo de compra. Escalões de serviço baseado em DTU passe de sete dias para o escalão básico, 35 dias para outros escalões.
- Pode restaurar uma base de dados para um ponto anterior no tempo dentro do período de retenção. Também pode restaurar uma base de dados eliminada, restaure para uma região geográfica diferente ou restaurar a partir de uma cópia de segurança de longa duração se a base de dados tem uma política de retenção de longo prazo (LTR).


![Cópia de segurança SQL do Azure](./media/migrate-best-practices-security-management/sql-backup.png)
*cópia de segurança do SQL do Azure*

**Saiba mais:**
- [Cópias de segurança automatizadas](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) para base de dados SQL.
- [Recuperar uma base de dados](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) através de cópias de segurança automatizadas.

 
#### <a name="back-up-azure-functions"></a>Criar cópias de segurança das funções do Azure

Uma vez que as funções do Azure funciona mais ou menos como código, deve criá-los cópias de segurança utilizando os mesmos métodos que utilizar para proteger o código como o controlo de origem no GitHub ou serviços de DevOps do Azure

**Saiba mais:**

- [Proteção de dados](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts) para DevOps do Azure.

## <a name="best-practice-set-up-disaster-recovery"></a>Prática recomendada: Configurar a recuperação após desastre 

Além de proteger dados, o planejamento de BCDR deve considerar como manter as aplicações e cargas de trabalho disponíveis em caso de desastre. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Configurar a recuperação após desastre para aplicações de IaaS

Para cargas de trabalho em execução em VMs IaaS do Azure e o armazenamento do Azure, considere estas soluções:

- **Azure Site Recovery**: Orquestra a replicação de VMs do Azure num site primário para uma região secundária. Quando ocorrem falhas, efetuar a ativação pós-falha da região primária para o secundário e os utilizadores podem continuar a aceder às aplicações. Quando as coisas estão de volta ao normal, reativação pós-falha para a região primária.
- **O armazenamento do Azure**: O Azure fornece resiliência na compilação e de elevada disponibilidade para diferentes tipos de armazenamento:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

O Azure Site Recovery é o principal serviço do Azure para garantir que as VMs do Azure podem ser colocadas online e disponibilizadas quando ocorrerem falhas de aplicações VM. O site Recovery replica as VMs de um site primário para a região secundária do Azure. Momentos de desastre, pós-falha das VMs da região primária e continuar acessando-los como normal na região secundária. Quando as operações retornam ao normal, pode efetuar a ativação pós-falha de VMs para a região primária.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*recuperação de sites*

**Saiba mais:**
- [Revisão](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) cenários de recuperação após desastre para VMs do Azure.
- [Saiba como](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) configurar recuperação após desastre para uma VM do Azure após a migração.

#### <a name="azure-storage"></a>Storage do Azure

O armazenamento do Azure é replicado para elevada disponibilidade e resiliência de incorporada.

-   **Armazenamento georredundante (GRS)**: Protege contra uma falha de toda a região, com, pelo menos, 99,99999999999999% (16 9 s) a durabilidade dos objetos ao longo de um determinado ano.
    - Replica os dados de armazenamento para a região secundária com o qual a região primária está emparelhado.
    - Se a região primária fica inativo, e a Microsoft inicie uma ativação pós-falha para a região secundária, terá acesso de leitura aos seus dados.
- **Armazenamento georredundante com acesso de leitura (RA-GRS)**: Protege contra uma falha de toda a região.
    - Replica os dados de armazenamento para a região secundária.
    - Tem a garantia de acesso de leitura para os dados replicados na região secundária, independentemente de estar ou não a Microsoft inicie uma ativação pós-falha. onde os dados de dois ou mais datacenters na mesma região, poderão ter um problema, mas os dados ainda estão disponíveis numa região geograficamente dispersas.
-   **Zona (ZRS) de armazenamento com redundância de**:  Protege contra falhas de datacenter.
    - O ZRS replica dados de forma síncrona em três clusters de armazenamento numa única região. Clusters e fisicamente separados e cada localizado na sua própria zona de disponibilidade.
    - Se ocorrer um desastre, o armazenamento continuarão a estar disponível. O ZRS deve ser o destino mínimo para cargas de trabalho de missão crítica.



**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/common/storage-redundancy) os replicação de armazenamento do Azure.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Configurar a recuperação após desastre para cargas de trabalho de PaaS

Vamos considerar desastre opções de recuperação para os nossos exemplos de carga de trabalho PaaS.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Recuperação após desastre de servidor SQL do Azure

Existem várias opções diferentes, cada um com impacto na perda de dados, o tempo de recuperação e o custo.

Pode usar grupos de ativação pós-falha e a georreplicação ativa para fornecer resiliência contra falhas regionais e de falhas catastróficas

- **Replicação geográfica activa**: Implemente a georreplicação ativa para recuperação após desastre rápida se ocorrer uma falha do datacenter, ou não é possível estabelecer uma ligação para uma base de dados primária.
    - Replicação geográfica continuamente cria réplicas legíveis da base de dados em até quatro bases de dados secundárias nas regiões idêntica ou diferentes.
    - Uma paralisação, a ativação pós-falha para uma das regiões do secundários e colocar a base de dados online.
- **Grupos de ativação pós-falha automática**: Grupos de ativação pós-falha automática estendem a georreplicação ativa com ativação pós-falha transparente de várias bases de dados.
    - Um grupo de ativação pós-falha automática fornece uma abstração eficaz de georreplicação ativa com a replicação de base de dados de nível de grupo e a ativação pós-falha automática.
    - Criar um grupo de ativação pós-falha que contenha um servidor primário que aloja uma ou mais bases de dados primárias, um servidor secundário que aloja as réplicas só de leitura das bases de dados primárias, serviços de escuta que apontam para cada servidor e uma política de ativação pós-falha automática.
    - Os pontos de extremidade do serviço de escuta especificado eliminam a necessidade de alterar a cadeia de ligação SQL após a ativação pós-falha.
- **O restauro geográfico**: 
    -   O restauro geográfico permite-lhe recuperar a base de dados para uma região diferente. A cópia de segurança automatizada de todas as bases de dados do Azure será replicada para uma região secundária em segundo plano. Sempre-lo irá restaurar a base de dados a partir da cópia de ficheiros de cópia de segurança armazenados na região secundária.
-   **Bases de dados com redundância de zona** fornecem suporte interno para as zonas de disponibilidade do Azure.
    - Bases de dados com redundância de zona melhoram a elevada disponibilidade para o Azure SQL Server em caso de falha do Centro de dados.
    - Com redundância de zona, pode colocar as réplicas de base de dados com redundância de dentro de diferentes zonas de disponibilidade numa região. 



![Replicação geográfica](./media/migrate-best-practices-security-management/geo-replication.png)
*georreplicação*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) elevada disponibilidade para o Azure SQL Server.
- [Leitura](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) 101 de bases de dados SQL do Azure para recuperação após desastre.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) de grupos de georreplicação e ativação pós-falha Active Directory.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) conceção para recuperação após desastre.
- [Obtenha as práticas recomendadas](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) para grupos de ativação pós-falha.
- [Obtenha as práticas recomendadas](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) para segurança após o restauro geográfico ou de ativação pós-falha.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) redundância de zona
- [Saiba como](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) executar um teste de recuperação após desastre para base de dados SQL.

### <a name="disaster-recovery-for-azure-functions"></a>Recuperação após desastre para as funções do Azure

Se a infraestrutura de computação no Azure falhar, uma aplicação de função do Azure pode ficar indisponível.

- Para minimizar a possibilidade de tal tempo de inatividade, utilize duas aplicações de funções implementadas em regiões diferentes.
- O Gestor de tráfego do Azure pode ser configurado para detetar problemas na aplicação de função principal e redirecionar automaticamente o tráfego para a aplicação de funções na região secundária
- O Gestor de tráfego com o armazenamento com redundância geográfica permite-lhe ter a mesma função em várias regiões, em caso de falha regional


![O Gestor de tráfego](./media/migrate-best-practices-security-management/traffic-manager.png)
*Gestor de tráfego*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) recuperação após desastre para aplicações do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) recuperação após desastre e distribuição geográfica para as funções do Azure duráveis.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Prática recomendada: Utilizar discos geridos e conjuntos de disponibilidade

Para agrupar VMs e para isolar as VMs num conjunto a partir de outros recursos, o Azure utiliza conjuntos de disponibilidade. As VMs num conjunto de disponibilidade são distribuídas por vários domínios de falha com subsistemas separados, para proteger contra falhas locais e também são distribuídas em vários domínios de atualização para que nem todas as VMs num conjunto de reinício ao mesmo tempo.

Discos geridos pelo Azure simplificam a gestão de discos para VMs de IaaS do Azure, ao gerir as contas de armazenamento associadas aos discos VM.

- Recomendamos que utilize discos geridos, sempre que possível. Apenas é necessário especificar o tipo de armazenamento que pretende utilizar e o tamanho do disco tem de, e o Azure cria e gere o disco por si, em segundo plano.
- Pode converter discos existentes para gerido.
- Deve criar VMs nos conjuntos de disponibilidade para disponibilidade e resiliência de elevado. Quando ocorrem falhas não planeadas ou não planeada, conjuntos de disponibilidade garantem que pelo menos uma das suas VMs no conjunto de continua a estar disponível.


![Discos geridos](./media/migrate-best-practices-security-management/managed-disks.png)
*discos geridos*

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) de discos geridos.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) conversão de discos geridos.
- [Saiba como](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) gerir a disponibilidade de VMs do Windows no Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Prática recomendada: Monitorizar a utilização de recursos e desempenho 

Poderá Moveu as cargas de trabalho para o Azure para as suas capacidades de dimensionamento de grande. No entanto, mover a sua carga de trabalho não significa que Azure irá implementar automaticamente Dimensionar sem sua entrada. Por exemplo:

- Se sua organização de marketing, envia um novo anúncio de TV que orienta o tráfego de mais de 300%, isso poderia causar problemas de disponibilidade do site. A carga de trabalho recentemente migrada poderá apresentar falhas e de limites atribuídos.
- Outro exemplo poderá ser um ataque de (DDoS) distribuído do denial-of-service na carga de trabalho migrada. Neste caso não vai querer aumentar, mas para impedir que a origem dos ataques de chegar aos seus recursos.

Estes dois casos tem resoluções diferentes, mas para ambos os que precisam de uma visão geral de que está a acontecer com monitorização de desempenho e de utilização.

- Monitor do Azure pode ajudar a estas métricas de superfície e fornecer a resposta com alertas, dimensionamento automático, os hubs de eventos, aplicações lógicas e muito mais.
- Para além de monitorização do Azure, pode integrar o seu aplicativo de SIEM de terceiros para monitorizar os registos do Azure para eventos de auditoria e de desempenho.


![O Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Monitor do Azure*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-monitor/overview) Monitor do Azure.
- [Obtenha as práticas recomendadas](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) para monitorização e diagnóstico.
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) dimensionamento automático.
- [Saiba como](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) encaminhar os dados do Azure para uma ferramenta SIEM.

## <a name="best-practice-enable-diagnostic-logging"></a>Prática recomendada: Ativar o registo de diagnósticos

Recursos do Azure geram um número considerável de dados de telemetria e métricas de registo.

- Por predefinição, a maioria dos tipos de recursos não tem o registo de diagnósticos ativado.
- Ao ativar o registo de diagnósticos todos os seus recursos, pode consultar os dados de registo e criar alertas e playbooks com base no mesmo.
- Quando ativar o registo de diagnósticos, cada recurso terão um conjunto específico de categorias. Selecione uma ou mais categorias de registo e uma localização para os dados de registo. Os registos podem ser enviados para uma conta de armazenamento, hub de eventos, ou para os registos do Azure Monitor. 


![Registo de diagnósticos](./media/migrate-best-practices-security-management/diagnostics.png)
*registo de diagnósticos*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) recolher e consumir dados de registo.
- [Saiba o que é suportado](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) para registo de diagnósticos.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Prática recomendada: Configurar alertas e playbooks

Com o registo de diagnóstico ativado para recursos do Azure, pode começar a utilizar dados de registo para criar alertas personalizados.

- Alertas de forma proativa notificá-lo quando condições são encontradas nos seus dados de monitorização. Em seguida, pode solucionar problemas antes dos utilizadores do sistema tenha em atenção-los. Pode alertar sobre coisas como valores de métrica, consultas de pesquisa de registos, eventos de registo de atividade, estado de funcionamento da plataforma e a disponibilidade de Web site.
- Quando são acionados alertas, pode executar um Playbook de aplicação lógica. Um playbook ajuda-o a automatizar e orquestrar uma resposta a um alerta específico. Playbooks baseiam-se no Azure Logic Apps. Pode utilizar modelos de aplicação lógica para criar playbooks, ou criar os seus próprios.
- Como um exemplo simples, pode criar um alerta que é acionada quando uma verificação de porta acontece em relação a um grupo de segurança de rede.  Pode configurar um playbook que é executado e bloqueia o endereço IP de origem a análise.
- Outro exemplo poderá ser uma aplicação com um vazamento de memória.  Quando obtém a utilização da memória até um certo ponto, um playbook pode reciclar o processo.


![Alertas](./media/migrate-best-practices-security-management/alerts.png)
*alertas*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) alertas.
- [Saiba mais sobre](https://docs.microsoft.com/azure/security-center/security-center-playbooks) playbooks de segurança que respondam aos alertas do Centro de segurança.

## <a name="best-practice-use-the-azure-dashboard"></a>Prática recomendada: Utilize o dashboard do Azure

O portal do Azure é uma consola de unificado e baseado na web que permite-lhe criar, gerir e monitorizar tudo, desde aplicações web simples a aplicações na cloud complexas. Inclui um dashboard personalizável e opções de acessibilidade.
- Pode criar vários dashboards e partilhá-los com outras pessoas que têm acesso às suas subscrições do Azure.
- Com esse modelo partilhado, a sua equipa tem visibilidade para o ambiente do Azure, permitindo que eles sejam proativa ao gerenciamento de sistemas na cloud.


![Dashboard do Azure](./media/migrate-best-practices-security-management/dashboard.png)
*dashboard do Azure*

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) criar um dashboard.
- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) estrutura do dashboard.


## <a name="best-practice-understand-support-plans"></a>Prática recomendada: Compreender os planos de suporte

Em algum momento precisará de colaborar com a sua equipa de suporte técnico ou à equipe de suporte da Microsoft. É vital ter um conjunto de políticas e procedimentos para obter suporte durante a cenários como a recuperação após desastre. Além disso, sua equipe de suporte e os administradores deve receber formação sobre como implementar essas políticas.

- No improvável evento que um problema de serviço do Azure afeta sua carga de trabalho, os administradores devem saber como submeter um pedido de suporte para a Microsoft da forma mais adequado e eficiente.
- Familiarize-se com vários planos de suporte oferecidos para o Azure. Eles variam de tempos de resposta dedicados às instâncias de desenvolvedor, para o Premier suporte com um tempo de resposta de menos de 15 minutos.


![Planos de suporte](./media/migrate-best-practices-security-management/support.png)
*planos de suporte*

**Saiba mais:**
- [Obtenha uma visão geral](https://azure.microsoft.com/support/options/) planos de suporte do Azure.
- [Saiba mais sobre](https://azure.microsoft.com/support/legal/sla/) (SLAs) de contratos de nível de serviço.

## <a name="best-practice-manage-updates"></a>Prática recomendada: Gerir atualizações

Manter as VMs do Azure atualizada com o sistema operativo mais recente e atualizações de software é um fardo em massa. A capacidade de todas as VMs, para que eles precisam de atualizações e para emitir automaticamente essas atualizações é extremamente valiosa de superfície.

- Pode utilizar a gestão de atualizações na automatização do Azure para gerir atualizações de sistema operativo para computadores que executam a computadores Windows e Linux que são implementadas no Azure, no local e em outros fornecedores de cloud. 
- Utilizar gestão de atualizações para avaliar rapidamente o estado das atualizações disponíveis em todos os computadores agente e gerir a instalação da atualização.
- Pode ativar a gestão de atualizações para as VMs diretamente a partir de uma conta de automatização do Azure. Também pode atualizar uma única VM a partir da página VM no portal do Azure.
- Além disso, as VMs do Azure pode ser registadas com o System Center Configuration Manager. Em seguida, pode migrar a carga de trabalho do Configuration Manager para o Azure e fazer atualizações de software e de relatórios a partir de uma interface web único.


![Atualizações VM](./media/migrate-best-practices-security-management/updates.png)
*atualizações*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-update-management) gerenciamento de atualizações no Azure.
- [Saiba como](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) integrar Configuration Manager com a gestão de atualizações.
- [Perguntas mais frequentes sobre](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) sobre o Configuration Manager no Azure.


## <a name="implement-a-change-management-process"></a>Implementar um processo de gerenciamento de alterações

Tal como acontece com qualquer sistema de produção, tornando a qualquer tipo de alteração pode afetar o seu ambiente. Um processo de gestão de alteração que exige pedidos para serem submetidas para fazer alterações em sistemas de produção é um valioso acréscimo no seu ambiente migrado.

- Pode criar as estruturas de prática para gerenciamento de alterações gerar o reconhecimento de administradores e suporte técnico.
- Pode utilizar a automatização do Azure para ajudar com a gestão de configuração e registo de alterações para os fluxos de trabalho migrados.
- Quando a imposição de processo de gestão de alterações, pode utilizar os registos de auditoria para ligar a alteração do Azure pedidos de alteração de registos existente supostamente (ou não). Para que se vir uma alteração efetuada sem um pedido de alteração correspondente, pode investigar o que correu mal no processo.

O Azure tem uma solução de controlo de alterações na automatização do Azure:

- A solução controla as alterações ao software do Windows e Linux e arquivos, chaves de registo do Windows, serviços do Windows e dos daemons do Linux.
- As alterações nos servidores monitorizados são enviadas para o serviço do Azure Monitor na cloud para processamento.
- Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados.
- No dashboard do controlo de alterações, pode ver facilmente as alterações efetuadas na sua infraestrutura de servidor.


![Gerenciamento de alterações](./media/migrate-best-practices-security-management/change.png)
*gerenciamento de alterações*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-change-tracking) controlo de alterações.
- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-intro) capacidades de automatização do Azure.




## <a name="next-steps"></a>Passos Seguintes 

Reveja a outras práticas recomendadas:


- [Melhores práticas](migrate-best-practices-networking.md) para funcionamento em rede após a migração.
- [Melhores práticas](migrate-best-practices-costs.md) do cost management após a migração.








