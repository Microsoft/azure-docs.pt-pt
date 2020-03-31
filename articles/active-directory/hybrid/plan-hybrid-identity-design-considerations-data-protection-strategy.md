---
title: Design de identidade híbrida - estratégia de proteção de dados Azure [ Microsoft Docs
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
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109358"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Defina a estratégia de proteção de dados para a sua solução de identidade híbrida
Nesta tarefa, irá definir a estratégia de proteção de dados para a sua solução de identidade híbrida para satisfazer os requisitos de negócio que definiu:

* [Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar requisitos do controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Tal como explicado nos requisitos de sincronização do [diretório Determine,](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)o Microsoft Azure AD pode sincronizar-se com os seus Serviços de Domínio de Diretório Ativo (AD DS) no local. Esta integração permite que as organizações utilizem a AD Azure para verificar as credenciais dos utilizadores quando estão a tentar aceder aos recursos corporativos. Pode fazê-lo para ambos os cenários: dados em repouso no local e na nuvem. O acesso aos dados em AD Azure requer a autenticação do utilizador através de um serviço de token de segurança (STS).

Uma vez autenticado, o nome principal do utilizador (UPN) é lido a partir do símbolo de autenticação. Em seguida, o sistema de autorização determina a partilha e o recipiente replicados correspondentes ao domínio do utilizador. A informação sobre a existência do utilizador, o estado habilitado e a função ajudam o sistema de autorização a determinar se o acesso ao inquilino-alvo está autorizado para o utilizador nessa sessão. Certas ações autorizadas (especificamente, criar o utilizador e o reset de passwords) criam um rasto de auditoria que um administrador de inquilino usa para gerir os esforços de conformidade ou investigações.

A transferência de dados do seu datacenter no local para o Armazenamento Do Azure através de uma ligação à Internet pode nem sempre ser viável devido ao volume de dados, disponibilidade de largura de banda ou outras considerações. O Serviço de [Importação/Exportação](../../storage/common/storage-import-export-service.md) de Armazenamento Azure oferece uma opção baseada em hardware para a colocação/recuperação de grandes volumes de dados no armazenamento de blob. Permite-lhe enviar discos rígidos [encriptados bitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) diretamente para um centro de dados Azure onde os operadores de nuvem fazem o upload do conteúdo para a sua conta de armazenamento, ou podem descarregar os seus dados Azure para as suas unidades para lhe devolver. Apenas são aceites discos encriptados para este processo (utilizando uma chave BitLocker gerada pelo próprio serviço durante a configuração do trabalho). A chave BitLocker é fornecida ao Azure separadamente, proporcionando assim a partilha de teclas da banda.

Uma vez que os dados em trânsito podem ocorrer em diferentes cenários, também é relevante saber que o Microsoft Azure utiliza [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos inquilinos uns dos outros, empregando medidas como firewalls de nível de anfitrião e de hóspedes, filtragem de pacotes IP, bloqueio de portas e pontos finais HTTPS. No entanto, a maioria das comunicações internas do Azure, incluindo infraestruturas e infraestruturas para clientes (no local), também estão encriptadas. Outro cenário importante são as comunicações dentro dos datacenters do Azure; A Microsoft gere as redes para garantir que nenhum VM pode personificar ou escutar o endereço IP de outro. TLS/SSL é utilizado ao aceder a bases de dados de Armazenamento Azure ou SQL, ou quando se conecta aos Serviços cloud. Neste caso, o administrador do cliente é responsável pela obtenção de um certificado TLS/SSL e implantando-o na sua infraestrutura de inquilinos. O tráfego de dados que se desloca entre Máquinas Virtuais na mesma implementação ou entre inquilinos numa única implementação através da Rede Virtual Microsoft Azure pode ser protegido através de protocolos de comunicação encriptados, tais como HTTPS, SSL/TLS, ou outros.

Dependendo da forma como respondeu às questões nos requisitos de proteção de [dados determine,](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)deve ser capaz de determinar como pretende proteger os seus dados e como a solução de identidade híbrida pode ajudá-lo nesse processo. O quadro seguinte mostra as opções suportadas pelo Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Em repouso na nuvem | Em repouso no local | Em trânsito |
| --- | --- | --- | --- |
| Encriptação de Unidade BitLocker |X |X | |
| SQL Server para encriptar bases de dados |X |X | |
| Encriptação VM-to-VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leia [compliance por funcionalidade](https://azure.microsoft.com/support/trust-center/services/) no Microsoft [Azure Trust Center](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações com as quais cada serviço Azure está em conformidade.
> Uma vez que as opções de proteção de dados utilizam uma abordagem multicamada, a comparação entre essas opções não é aplicável a esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada estado dos dados.
>
>

## <a name="define-content-management-options"></a>Definir opções de gestão de conteúdos

Uma vantagem de usar a AD Azure para gerir uma infraestrutura de identidade híbrida é que o processo é totalmente transparente do ponto de vista do utilizador final. O utilizador tenta aceder a um recurso partilhado, o recurso requer autenticação, o utilizador tem de enviar um pedido de autenticação à AD Azure para obter o token e aceder ao recurso. Todo este processo acontece em segundo plano, sem interação do utilizador. 

As organizações que estão preocupadas com a privacidade dos dados geralmente requerem classificação de dados para a sua solução. Se a sua atual infraestrutura no local já estiver a utilizar a classificação de dados, é possível utilizar a AD Azure como principal repositório para a identidade do utilizador. Uma ferramenta comum que é usada no local para a classificação de dados chama-se Toolkit de [Classificação](https://msdn.microsoft.com/library/Hh204743.aspx) de Dados para Windows Server 2012 R2. Esta ferramenta pode ajudar a identificar, classificar e proteger dados em servidores de ficheiros na sua nuvem privada. Também é possível utilizar a [Classificação Automática](https://technet.microsoft.com/library/hh831672.aspx) de Ficheiros no Windows Server 2012 para realizar esta tarefa.

Se a sua organização não tiver classificação de dados no lugar, mas precisar de proteger ficheiros sensíveis sem adicionar novos Servidores no local, podem utilizar o [Microsoft Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  O Azure RMS utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mails, e funciona em vários dispositivos - telefones, tablets e Computadores. Como o Azure RMS é um serviço na nuvem, não há necessidade de configurar explicitamente os fundos com outras organizações antes de poder partilhar conteúdo protegido com eles. Se estas já possuírem um Office 365 ou um diretório do Azure AD, a colaboração entre as organizações é suportada automaticamente. Também pode sincronizar apenas os atributos de diretório que o Azure RMS necessita para suportar uma identidade comum para as suas contas de Diretório Ativo no local, utilizando os Serviços de Sincronização de Sincronização de Diretórios Ativos azure (AAD Sync) ou Azure AD Connect.

Uma parte vital da gestão de conteúdos é compreender quem está a aceder a que recurso, pelo que uma rica capacidade de exploração é importante para a solução de gestão de identidade. A Azure AD fornece registo ao longo de 30 dias, incluindo:

* Alterações na adesão a papéis (ex: utilizador adicionado ao papel global de administrador)
* Atualizações credenciais (ex: alterações de palavra-passe)
* Gestão de domínio (ex: verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão do utilizador (ex: adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

> [!NOTE]
> Leia o [Microsoft Azure Security and Audit Log Management](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre as capacidades de exploração de madeira no Azure.
> Dependendo da forma como respondeu às questões nos requisitos de gestão de [conteúdos determine,](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)deverá ser capaz de determinar como pretende que o conteúdo seja gerido na sua solução de identidade híbrida. Embora todas as opções expostas no Quadro 6 sejam capazes de se integrar em Azure AD, é importante definir o que é mais adequado para as suas necessidades de negócio.
>
>

| Opções de gestão de conteúdos | Vantagens | Desvantagens |
| --- | --- | --- |
| Centralizado no local (Ative Directory Rights Management Server) |Controlo total sobre a infraestrutura do servidor responsável pela classificação dos dados <br> Capacidade incorporada no Windows Server, sem necessidade de licença extra ou subscrição <br> Pode ser integrado com a Azure AD num cenário híbrido <br> Suporta capacidades de gestão de direitos de informação (IRM) em serviços Microsoft Online, como Exchange Online e SharePoint Online, bem como Office 365 <br> Suporta no local os produtos do servidor microsoft, tais como Exchange Server, SharePoint Server e servidores de ficheiros que executam o Windows Server e a Infraestrutura de Classificação de Ficheiros (FCI). |Manutenção mais elevada (acompanhe as atualizações, configuração e potenciais atualizações), uma vez que o IT possui o Servidor <br> Requerem uma infraestrutura de servidor no local<br> Não aproveita as capacidades do Azure de forma nativa |
| Centralizado na nuvem (Azure RMS) |Mais fácil de gerir em comparação com a solução no local <br> Pode ser integrado com DS AD num cenário híbrido <br>  Totalmente integrado com a Azure AD <br> Não requer um servidor no local para implementar o serviço <br> Suporta no local produtos do servidor microsoft tais como Exchange Server, SharePoint, Server e servidores de ficheiros que executam o Windows Server e a Classificação de Ficheiros, Infraestrutura (FCI) <br> IT, pode ter controlo total sobre a chave do seu inquilino com capacidade BYOK. |A sua organização deve ter uma subscrição em nuvem que suporte o RMS <br> A sua organização deve ter um diretório Azure AD para apoiar a autenticação do utilizador para RMS |
| Híbrido (Azure RMS integrado com, On-Premises Ative Directory Management Management Server) |Este cenário acumula as vantagens de ambos, centralizados no local e na nuvem. |A sua organização deve ter uma subscrição em nuvem que suporte o RMS <br> A sua organização deve ter um diretório Azure AD para apoiar a autenticação do utilizador para RMS, <br> Requer uma ligação entre o serviço de nuvem Azure e a infraestrutura no local |

## <a name="define-access-control-options"></a>Definir opções de controlo de acesso
Aproveitando as capacidades de autenticação, autorização e controlo de acesso disponíveis no Azure AD, pode permitir à sua empresa utilizar um repositório de identidade central, permitindo que utilizadores e parceiros utilizem um único sinal (SSO) como mostra a seguinte figura:

![gestão centralizada](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gestão centralizada e integração plena com outros diretórios

O Azure Ative Directory fornece um único sign-on a milhares de aplicações SaaS e aplicações web no local. Consulte a lista de compatibilidade da Federação de [Diretórios Ativos do Azure: fornecedores de identidade de terceiros que podem ser usados para implementar um único](how-to-connect-fed-compatibility.md) artigo de inscrição para mais detalhes sobre o terceiro sso que foram testados pela Microsoft. Esta capacidade permite à organização implementar uma variedade de cenários B2B, mantendo o controlo da identidade e gestão de acesso. No entanto, durante o processo de conceção b2B, é importante compreender o método de autenticação que é utilizado pelo parceiro e validar se este método for suportado pelo Azure. Atualmente, os seguintes métodos são suportados pela Azure AD:

* Linguagem de marcação de afirmação de segurança (SAML)
* OAuth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> ler Protocolos de [Autenticação de Diretório Ativo Azure](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e as suas capacidades no Azure.
>
>

Utilizando o suporte da AD Azure, as aplicações de negócios móveis podem usar a mesma experiência de autenticação fácil dos Serviços Móveis para permitir que os colaboradores assinem as suas aplicações móveis com as suas credenciais de Diretório Ativo corporativo. Com esta funcionalidade, a Azure AD é suportada como fornecedora de identidade em Serviços Móveis ao lado dos outros fornecedores de identidade já suportados (que incluem Contas Microsoft, ID do Facebook, ID do Google e ID do Twitter). Se as aplicações no local utilizarem a credencial do utilizador localizada no AD DS da empresa, o acesso de parceiros e utilizadores provenientes da nuvem deve ser transparente. Você pode gerir o controlo de acesso condicional do utilizador a aplicações web (baseadas na nuvem) web, web API, serviços de nuvem Microsoft, aplicações SaaS de terceiros e aplicações de clientes nativos (móveis), e ter os benefícios de segurança, auditoria, reportando tudo em uma lugar. No entanto, recomenda-se validar a implementação num ambiente de não produção ou com um número limitado de utilizadores.

> [!TIP]
> é importante referir que a Azure AD não tem política de grupo como a AD DS tem. Para impor a política dos dispositivos, necessita de uma solução de gestão de dispositivos móveis, como o [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Uma vez que o utilizador é autenticado utilizando o Azure AD, é importante avaliar o nível de acesso que o utilizador tem. O nível de acesso que o utilizador tem sobre um recurso pode variar. Embora o Azure AD possa adicionar uma camada de segurança adicional controlando o acesso a alguns recursos, lembre-se que o próprio recurso também pode ter a sua própria lista de controlo de acesso separadamente, como o controlo de acesso para ficheiros localizados num Servidor de Ficheiros. A figura que se segue resume os níveis de controlo de acesso que pode ter num cenário híbrido:

![controlo de acesso](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na Figura X representa um cenário de controlo de acesso que pode ser coberto por Azure AD. Abaixo tem uma descrição de cada cenário:

1. Acesso Condicional a aplicações alojadas no local: Pode utilizar dispositivos registados com políticas de acesso para aplicações configuradas para utilizar AD FS com Windows Server 2012 R2.

2. Controlo de Acesso ao portal Azure: O Azure também permite controlar o acesso ao portal utilizando o controlo de acesso baseado em funções (RBAC)). Este método permite à empresa restringir o número de operações que um indivíduo pode fazer no portal Azure. Ao utilizar o RBAC para controlar o acesso ao portal, os administradores de TI podem delegar o acesso utilizando as seguintes abordagens de gestão de acesso:

   - Atribuição de funções baseada em grupo: Pode atribuir acesso a grupos AD Azure que podem ser sincronizados a partir do seu Diretório Ativo local. Isto permite-lhe aproveitar os investimentos existentes que a sua organização tem feito em ferramentas e processos para gerir grupos. Também pode utilizar a funcionalidade de gestão de grupo seletiva do Azure AD Premium.
   - Utilize funções incorporadas no Azure: Pode utilizar três funções — Proprietário, Colaborador e Leitor, para garantir que os utilizadores e grupos tenham autorização para fazer apenas as tarefas de que necessitam para fazerem o seu trabalho.
   -  Acesso granular aos recursos: Pode atribuir funções a utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso Azure individual, como um website ou base de dados. Desta forma, pode garantir que os utilizadores têm acesso a todos os recursos de que necessitam e sem acesso a recursos que não necessitam de gerir.

   > [!NOTE]
   > Se estiver a construir aplicações e pretender personalizar o controlo de acesso para as mesmas, também é possível utilizar funções de aplicação azure AD para autorização. Reveja este [exemplo WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sobre como construir a sua aplicação para usar esta capacidade.


3. Acesso Condicional para aplicações do Office 365 com o Microsoft Intune: Os administradores de TI podem fornecer políticas de dispositivos de acesso condicional para proteger recursos corporativos, permitindo ao mesmo tempo que os trabalhadores da informação em dispositivos conformes acedam aos serviços. 
  
4. Acesso Condicional para aplicações Saas: [Esta funcionalidade](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) permite configurar regras de acesso a vários fatores por aplicação e a capacidade de bloquear o acesso a utilizadores que não estão numa rede fidedigna. Pode aplicar as regras de autenticação de vários fatores a todos os utilizadores que estejam atribuídos à aplicação, ou apenas a utilizadores dentro de grupos de segurança especificados. Os utilizadores podem ser excluídos do requisito de autenticação de vários fatores se acederem à aplicação a partir de um endereço IP que dentro da rede da organização.

Uma vez que as opções de controlo de acesso utilizam uma abordagem multicamada, a comparação entre essas opções não é aplicável a esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada cenário que o exija para controlar o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta a incidentes
A Azure AD pode ajudar a identificar potenciais riscos de segurança no ambiente, monitorizando a atividade do utilizador. O IT pode utilizar relatórios de acesso e utilização da Azure AD para ganhar visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de TI pode determinar melhor onde possíveis riscos de segurança podem estar para que possam planear adequadamente mitigar esses riscos.  [A subscrição do Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que podem permitir que a TI obtenha esta informação. [Os relatórios da AD Azure](../reports-monitoring/overview-reports.md) são categorizados da seguinte forma:

* **Relatórios de anomalias**: Conter eventos de inscrição que foram considerados anómalos. O objetivo é torná-lo consciente de tal atividade e permitir-lhe fazer uma determinação sobre se um evento é suspeito.
* **Relatório de aplicação integrado**: Fornece informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. O Azure Ative Directory oferece integração com milhares de aplicações na nuvem.
* **Relatórios de erro**: Indicar erros que possam ocorrer ao fornecer contas a aplicações externas.
* **Relatórios específicos do utilizador**: Mostrar dados de atividade do dispositivo/iniciar a sua atividade para um utilizador específico.
* **Registos de atividades**: Contenha um registo de todos os eventos auditados nas últimas 24 horas, últimos 7 dias ou 30 dias, bem como alterações de atividade do grupo, e redefinição de passwords e atividade de registo.

> [!TIP]
> Outro relatório que também pode ajudar a equipa de Resposta a Incidentes a trabalhar num caso é o utilizador com um relatório de [credenciais vazado.](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) Este relatório apresenta qualquer correspondência entre a lista de credenciais vazadas e o seu inquilino.
>


Outros importantes relatórios incorporados em Azure AD que podem ser usados durante uma investigação de resposta a incidentes e são:

* Atividade de reset de **palavra-passe**: fornecer ao administrador informações sobre como o reset de palavra-passe está a ser usado ativamente na organização.
* A atividade de **redefinição de palavra-passe**: fornece informações sobre as quais os utilizadores registaram os seus métodos de reset de palavras-passe e quais os métodos que selecionou.
* **Atividade**do grupo : fornece um histórico de alterações ao grupo (ex: utilizadores adicionados ou removidos) que foram iniciados no Painel de Acesso.

Além da capacidade de reporte principal do Azure AD Premium que pode utilizar durante um processo de investigação de resposta a incidentes, a TI também pode aproveitar o Relatório de Auditoria para obter informações como:

* Alterações na adesão a papéis (por exemplo, utilizador adicionado ao papel global de administrador)
* Atualizações credenciais (por exemplo, alterações de palavra-passe)
* Gestão de domínio (por exemplo, verificando um domínio personalizado, removendo um domínio)
* Adicionar ou remover aplicações
* Gestão do utilizador (por exemplo, adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

Uma vez que as opções de resposta a incidentes utilizam uma abordagem multicamada, a comparação entre essas opções não é aplicável a esta tarefa. Certifique-se de que está a aproveitar todas as opções disponíveis para cada cenário que o exija utilizar a capacidade de reporte da AD Azure como parte do processo de resposta a incidentes da sua empresa.

## <a name="next-steps"></a>Passos seguintes
[Determinar tarefas híbridas de gestão da identidade](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)
