---
title: Design de identidade híbrida - estratégia de proteção de dados Azure | Microsoft Docs
description: Define a estratégia de proteção de dados para a sua solução de identidade híbrida para satisfazer os requisitos de negócio que definiu.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703857"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Defina a estratégia de proteção de dados para a sua solução de identidade híbrida
Nesta tarefa, definirá a estratégia de proteção de dados para a sua solução de identidade híbrida para satisfazer os requisitos de negócio definidos em:

* [Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar requisitos do controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Como explicado nos [requisitos de sincronização de diretório determine,](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)o Microsoft Azure AD pode sincronizar com os seus serviços de domínio de diretório ativo no local (DS AD). Esta integração permite que as organizações utilizem a Azure AD para verificar as credenciais dos utilizadores quando estão a tentar aceder aos recursos corporativos. Pode fazê-lo para ambos os cenários: dados em repouso no local e na nuvem. O acesso aos dados em Azure AD requer a autenticação do utilizador através de um serviço de fichas de segurança (STS).

Uma vez autenticado, o nome principal do utilizador (UPN) é lido a partir do token de autenticação. Em seguida, o sistema de autorização determina a partição replicada e o recipiente correspondente ao domínio do utilizador. Informações sobre a existência do utilizador, estado habilitado e função ajudam então o sistema de autorização a determinar se o acesso ao inquilino-alvo é autorizado para o utilizador nessa sessão. Algumas ações autorizadas (especificamente, criar o utilizador e a redefinição de password) criam um rasto de auditoria que um administrador de inquilino usa para gerir esforços de conformidade ou investigações.

A transferência de dados do seu datacenter no local para o Azure Storage através de uma ligação à Internet pode nem sempre ser viável devido ao volume de dados, disponibilidade de largura de banda ou outras considerações. O [Serviço de Importação/Exportação de Armazenamento Azure](../../import-export/storage-import-export-service.md) oferece uma opção baseada em hardware para a colocação/recuperação de grandes volumes de dados no armazenamento de bolhas. Permite-lhe enviar discos rígidos [encriptados bitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) diretamente para um centro de dados Azure onde os operadores de nuvem carregam o conteúdo para a sua conta de armazenamento, ou podem descarregar os seus dados Azure para as suas unidades para voltar a si. Apenas os discos encriptados são aceites para este processo (utilizando uma chave BitLocker gerada pelo próprio serviço durante a configuração do trabalho). A chave BitLocker é fornecida ao Azure separadamente, proporcionando assim a partilha de chaves de banda.

Uma vez que os dados em trânsito podem ocorrer em diferentes cenários, também é relevante saber que o Microsoft Azure utiliza [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos inquilinos uns dos outros, utilizando medidas como firewalls de nível de hospedeiro e de hóspedes, filtragem de pacotes IP, bloqueio de portas e pontos finais HTTPS. No entanto, a maioria das comunicações internas da Azure, incluindo infra-estruturas-infra-estruturas e infra-estruturas-a-cliente (no local), também são encriptadas. Outro cenário importante é as comunicações dentro dos datacenters da Azure; A Microsoft gere redes para garantir que nenhum VM pode fazer-se passar ou escutar o endereço IP de outro. O TLS/SSL é utilizado ao aceder a Bases de Dados de Armazenamento Azure ou SQL, ou ao ligar-se aos Serviços cloud. Neste caso, o administrador do cliente é responsável pela obtenção de um certificado TLS/SSL e pela sua implantação na sua infraestrutura de inquilinos. O tráfego de dados que se move entre máquinas virtuais na mesma implementação ou entre inquilinos numa única implementação através da Rede Virtual Microsoft Azure pode ser protegido através de protocolos de comunicação encriptados como HTTPS, SSL/TLS, ou outros.

Dependendo da forma como respondeu às perguntas nos [requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)determine, deverá ser capaz de determinar como pretende proteger os seus dados e como a solução de identidade híbrida pode ajudá-lo nesse processo. A tabela seguinte mostra as opções suportadas pelo Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Em repouso na nuvem | Em repouso no local | Em trânsito |
| --- | --- | --- | --- |
| Encriptação de Unidade BitLocker |X |X | |
| SQL Server para encriptar bases de dados |X |X | |
| Encriptação VM-para-VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leia [Compliance by Feature](https://azure.microsoft.com/support/trust-center/services/) no Microsoft [Azure Trust Center](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações com as quais cada serviço Azure está em conformidade.
> Uma vez que as opções de proteção de dados utilizam uma abordagem multicamadora, a comparação entre essas opções não é aplicável para esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada estado dos dados.
>
>

## <a name="define-content-management-options"></a>Definir opções de gestão de conteúdos

Uma vantagem de usar a Azure AD para gerir uma infraestrutura de identidade híbrida é que o processo é totalmente transparente do ponto de vista do utilizador final. O utilizador tenta aceder a um recurso partilhado, o recurso requer autenticação, o utilizador tem de enviar um pedido de autenticação ao Azure AD para obter o token e aceder ao recurso. Todo este processo acontece em segundo plano, sem interação do utilizador. 

As organizações que se preocupam com a privacidade dos dados geralmente requerem classificação de dados para a sua solução. Se a sua atual infraestrutura no local já estiver a utilizar a classificação de dados, é possível utilizar a Azure AD como principal repositório para a identidade do utilizador. Uma ferramenta comum que é usada no local para a classificação de dados é chamada [de Data Classification Toolkit](/previous-versions/tn-archive/hh204743(v=technet.10)) para Windows Server 2012 R2. Esta ferramenta pode ajudar a identificar, classificar e proteger dados em servidores de ficheiros na sua nuvem privada. Também é possível utilizar a [Classificação Automática de Ficheiros](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) no Windows Server 2012 para realizar esta tarefa.

Se a sua organização não tiver a classificação de dados no local, mas precisar de proteger ficheiros sensíveis sem adicionar novos Servidores no local, pode utilizar o Microsoft [Azure Rights Management Service](/azure/information-protection/what-is-azure-rms).  O Azure RMS utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mails, e funciona em vários dispositivos — telefones, tablets e Computadores. Como o Azure RMS é um serviço de nuvem, não há necessidade de configurar explicitamente confianças com outras organizações antes de poder partilhar conteúdo protegido com eles. Se já têm um Microsoft 365 ou um diretório AD Azure, a colaboração entre organizações é automaticamente suportada. Também pode sincronizar apenas os atributos do diretório de que o Azure RMS necessita para suportar uma identidade comum para as suas contas de Diretório Ativo no local, utilizando os Serviços de Sincronização do Diretório Ativo Azure (Azure AD Sync) ou Azure AD Connect.

Uma parte vital da gestão de conteúdos é compreender a quem está a aceder a que recurso, pelo que uma rica capacidade de exploração madeireira é importante para a solução de gestão de identidade. A Azure AD fornece log ao longo de 30 dias, incluindo:

* Alterações na adesão à função (ex: utilizador adicionado ao papel de Administrador Global)
* Atualizações credenciais (ex: alterações de senha)
* Gestão de domínios (ex: verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão do utilizador (ex: adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

> [!NOTE]
> Leia [a Microsoft Azure Security and Audit Log Management](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre as capacidades de registo em Azure.
> Dependendo da forma como respondeu às perguntas nos [requisitos de gestão de conteúdos,](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)deverá ser capaz de determinar como pretende que o conteúdo seja gerido na sua solução de identidade híbrida. Embora todas as opções expostas no Quadro 6 sejam capazes de se integrar com a Azure AD, é importante definir o que é mais adequado às necessidades do seu negócio.
>
>

| Opções de gestão de conteúdos | Vantagens | Desvantagens |
| --- | --- | --- |
| Instalações centralizadas no local (Ative Directory Rights Management Server) |Controlo total sobre a infraestrutura do servidor responsável pela classificação dos dados <br> Capacidade incorporada no Windows Server, sem necessidade de licença extra ou subscrição <br> Pode ser integrado com Azure AD em um cenário híbrido <br> Suporta capacidades de gestão de direitos de informação (IRM) em serviços Microsoft Online como Exchange Online e SharePoint Online, bem como Microsoft 365 <br> Suporta no local produtos de servidores da Microsoft, tais como Exchange Server, SharePoint Server e servidores de ficheiros que executam o Windows Server e a Infraestrutura de Classificação de Ficheiros (FCI). |Maior manutenção (acompanhe as atualizações, configuração e potenciais atualizações), uma vez que o IT é dono do Servidor <br> Requerer uma infraestrutura de servidor no local<br> Não aproveita as capacidades do Azure de forma nativa |
| Centralizado na nuvem (Azure RMS) |Mais fácil de gerir em comparação com a solução no local <br> Pode ser integrado com DS AD em um cenário híbrido <br>  Totalmente integrado com Azure AD <br> Não requer um servidor no local para implementar o serviço <br> Suporta no local produtos de servidores da Microsoft tais como Exchange Server, SharePoint, Server e servidores de ficheiros que executam o Windows Server e Classificação de Ficheiros, Infraestrutura (FCI) <br> TI, pode ter controlo total sobre a chave do seu inquilino com capacidade BYOK. |A sua organização deve ter uma subscrição em nuvem que suporte RMS <br> A sua organização deve ter um diretório AD Azure para apoiar a autenticação do utilizador para o RMS |
| Híbrido (Azure RMS integrado com, Servidor de Gestão de Direitos de Diretório Ativo no Local) |Este cenário acumula as vantagens de ambos, centralizados no local e na nuvem. |A sua organização deve ter uma subscrição em nuvem que suporte RMS <br> A sua organização deve ter um diretório AD Azure para apoiar a autenticação do utilizador para o RMS, <br> Requer uma ligação entre o serviço de nuvem Azure e a infraestrutura no local |

## <a name="define-access-control-options"></a>Defina opções de controlo de acesso
Aproveitando as capacidades de autenticação, autorização e controlo de acessos disponíveis no Azure AD, pode permitir à sua empresa utilizar um repositório de identidade central, permitindo que os utilizadores e parceiros utilizem uma única sindestrução (SSO), como indicado na seguinte figura:

![gestão centralizada](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gestão centralizada e plena integração com outros diretórios

O Azure Ative Directory fornece um único sign-on a milhares de aplicações SaaS e aplicações web no local. Consulte a [lista de compatibilidades da federação de diretórios ativos do Azure: fornecedores de identidade de terceiros que podem ser usados para implementar](how-to-connect-fed-compatibility.md) um único artigo de inscrição para mais detalhes sobre o terceiro SSO que foram testados pela Microsoft. Esta capacidade permite que a organização implemente uma variedade de cenários B2B, mantendo o controlo da identidade e da gestão de acessos. No entanto, durante o processo de conceção B2B, é importante compreender o método de autenticação utilizado pelo parceiro e validar se este método for suportado pelo Azure. Atualmente, os seguintes métodos são suportados pela Azure AD:

* Linguagem de marcação de afirmação de segurança (SAML)
* Oauth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> leia [protocolos de autenticação de diretório ativo Azure](/previous-versions/azure/dn151124(v=azure.100)) para saber mais detalhes sobre cada protocolo e as suas capacidades no Azure.
>
>

Utilizando o suporte Azure AD, as aplicações móveis de negócio podem usar a mesma experiência de autenticação fácil de Serviços Móveis para permitir que os colaboradores assinem nas suas aplicações móveis com as suas credenciais de Ative Directy corporativo. Com esta funcionalidade, o Azure AD é suportado como um fornecedor de identidade em Serviços Móveis ao lado de outros fornecedores de identidade já suportados (que incluem Contas Microsoft, ID do Facebook, ID do Google e ID do Twitter). Se as aplicações no local utilizarem a credencial do utilizador localizada no DS AD da empresa, o acesso de parceiros e utilizadores provenientes da nuvem deve ser transparente. Pode gerir o controlo de acesso condicional do utilizador a aplicações web (baseadas na nuvem), a API web, serviços na nuvem da Microsoft, aplicações SaaS de terceiros e aplicações de clientes nativas (móveis) e ter os benefícios da segurança, auditoria, reporte tudo num só local. No entanto, recomenda-se validar a implementação num ambiente não produtivo ou com um número limitado de utilizadores.

> [!TIP]
> é importante mencionar que a Azure AD não tem política de grupo como a AD DS tem. Para impor a política dos dispositivos, precisa de uma solução de gestão de dispositivos móveis, como o [Microsoft Intune.](/mem/intune/)
>
>

Uma vez que o utilizador é autenticado usando Azure AD, é importante avaliar o nível de acesso que o utilizador tem. O nível de acesso que o utilizador tem sobre um recurso pode variar. Embora o Azure AD possa adicionar uma camada de segurança adicional controlando o acesso a alguns recursos, tenha em mente que o próprio recurso também pode ter a sua própria lista de controlo de acesso separadamente, como o controlo de acesso para ficheiros localizados num Servidor de Ficheiros. O seguinte número resume os níveis de controlo de acesso que pode ter num cenário híbrido:

![controlo de acessos](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na Figura X representa um cenário de controlo de acesso que pode ser coberto pelo Azure AD. Abaixo tem uma descrição de cada cenário:

1. Acesso condicional a aplicações que estão hospedadas no local: Pode utilizar dispositivos registados com políticas de acesso para aplicações configuradas para utilizar O FS AD com o Windows Server 2012 R2.

2. Controlo de Acesso ao portal Azure: O Azure também permite controlar o acesso ao portal utilizando o controlo de acesso baseado em funções Azure (Azure RBAC)). Este método permite à empresa restringir o número de operações que um indivíduo pode fazer no portal Azure. Ao utilizar o Azure RBAC para controlar o acesso ao portal, os Administradores de TI podem delegar o acesso utilizando as seguintes abordagens de gestão de acesso:

   - Atribuição de funções baseada em grupo: Pode atribuir acesso a grupos AD Azure que podem ser sincronizados a partir do seu Diretório Ativo local. Isto permite-lhe alavancar os investimentos existentes que a sua organização tem feito em ferramentas e processos de gestão de grupos. Também pode utilizar a função de gestão de grupos delegada do Azure AD Premium.
   - Use funções incorporadas no Azure: Pode utilizar três funções — Proprietário, Colaborador e Leitor, para garantir que os utilizadores e grupos têm permissão para fazer apenas as tarefas que precisam para fazer o seu trabalho.
   -  Acesso granular aos recursos: Pode atribuir funções a utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso Azure individual, como um website ou base de dados. Desta forma, pode garantir que os utilizadores têm acesso a todos os recursos de que necessitam e sem acesso a recursos que não precisam de gerir.

   > [!NOTE]
   > Se estiver a construir aplicações e quiser personalizar o controlo de acesso para elas, também é possível utilizar as Funções de Aplicação AD AZure para autorização. Reveja este [exemplo WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sobre como construir a sua aplicação para utilizar esta capacidade.


3. Acesso Condicional para aplicações Microsoft 365 com Microsoft Intune: Os administradores de TI podem providenciar políticas de dispositivos de acesso condicional para garantir recursos corporativos, ao mesmo tempo que permitem aos trabalhadores da informação em dispositivos compatíveis aceder aos serviços. 
  
4. Acesso Condicional para aplicações Saas: [Esta funcionalidade](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) permite configurar regras de acesso à autenticação multi-fator por aplicação e a capacidade de bloquear o acesso aos utilizadores que não se encontrem numa rede fidedigna. Pode aplicar as regras de autenticação de vários fatores a todos os utilizadores que estejam atribuídos à aplicação, ou apenas aos utilizadores dentro de grupos de segurança especificados. Os utilizadores podem ser excluídos do requisito de autenticação de vários fatores se estiverem a aceder à aplicação a partir de um endereço IP que dentro da rede da organização.

Uma vez que as opções de controlo de acesso utilizam uma abordagem multicamadora, a comparação entre essas opções não é aplicável a esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada cenário que o exija controlar o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta a incidentes
O Azure AD pode ajudar a TI a identificar potenciais riscos de segurança no ambiente, monitorizando a atividade do utilizador. O TI pode usar relatórios de acesso e utilização Azure AD para ganhar visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de TI pode determinar melhor onde podem estar os riscos de segurança possíveis para que possam planear adequadamente esses riscos.  [A subscrição Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que podem permitir que a TI obtenha esta informação. [Os relatórios da AD da AZure](../reports-monitoring/overview-reports.md) são classificados da seguinte forma:

* **Relatórios de anomalia :** Conter eventos de entrada que foram considerados anómalos. O objetivo é dar-lhe a conhecer tal atividade e permitir-lhe determinar se um evento é suspeito.
* **Relatório integrado de aplicações**: Fornece informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. O Azure Ative Directory oferece integração com milhares de aplicações em nuvem.
* **Relatórios de erro**: Indique erros que possam ocorrer ao aussar contas a aplicações externas.
* **Relatórios específicos do utilizador**: Mostrar o dispositivo/sinal em dados de atividade para um utilizador específico.
* **Registos** de atividades : Contenha um registo de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias, ou dure 30 dias, bem como alterações de atividade em grupo, e atividade de reset e registo de passwords.

> [!TIP]
> Outro relatório que também pode ajudar a equipa de Resposta a Incidentes a trabalhar num caso é o [utilizador com credenciais vazadas.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) Este relatório aparece entre a lista de credenciais vazadas e o seu inquilino.
>


Outros relatórios importantes incorporados em Azure AD que podem ser usados durante uma investigação de resposta a incidentes e são:

* **Atividade de reset de palavra-passe**: forneça ao administrador informações sobre como o reset da palavra-passe está a ser utilizado na organização.
* **Atividade de registo de redefinição de palavra-passe**: fornece informações sobre quais os utilizadores que registaram os seus métodos para reposição de passwords e quais os métodos selecionados.
* **Atividade do grupo**: fornece um histórico de alterações ao grupo (ex: utilizadores adicionados ou removidos) que foram iniciados no Painel de Acesso.

Além da capacidade de reporte principal do Azure AD Premium que pode utilizar durante um processo de investigação de Resposta a Incidentes, a TI também pode aproveitar o Relatório de Auditoria para obter informações como:

* Alterações na adesão à função (por exemplo, utilizador adicionado ao papel de Administrador Global)
* Atualizações credenciais (por exemplo, alterações de senha)
* Gestão de domínio (por exemplo, verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão do utilizador (por exemplo, adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

Uma vez que as opções de resposta a incidentes utilizam uma abordagem multicamadora, a comparação entre essas opções não é aplicável para esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada cenário que o exija a utilizar a capacidade de reporte AZure AD como parte do processo de resposta a incidentes da sua empresa.

## <a name="next-steps"></a>Próximos passos
[Determinar tarefas híbridas de gestão de identidade](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)