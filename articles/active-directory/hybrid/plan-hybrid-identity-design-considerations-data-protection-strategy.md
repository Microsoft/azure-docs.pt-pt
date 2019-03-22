---
title: Design de identidade híbrida - estratégia de proteção de dados do Azure | Documentos da Microsoft
description: Definir a estratégia de proteção de dados para a sua solução de identidade híbrida cumprir os requisitos de negócios que definiu.
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
ms.date: 11/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5cad3f735494ee74156e22a9bf150911c36eb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091482"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir a estratégia de proteção de dados para a sua solução de identidade híbrida
Nesta tarefa, vai definir a estratégia de proteção de dados para a sua solução de identidade híbrida cumprir os requisitos de negócios que definiu no:

* [Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Determinar os requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Determinar os requisitos de controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Conforme explicado [determinar os requisitos de sincronização do directory](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD pode sincronizar com os serviços de domínio de diretório Active Directory no local (AD DS). Esta integração permite que as organizações utilizar o Azure AD para verificar as credenciais dos utilizadores quando eles estão a tentar aceder a recursos empresariais. Pode fazê-lo para os dois cenários: dados em rest no local e na cloud. Acesso a dados no Azure AD requer autenticação do utilizador através de um serviço de token de segurança (STS).

Uma vez autenticado, o nome principal de utilizador (UPN) é lidos a partir do token de autenticação. Em seguida, o sistema de autorização determina a partição replicada e o contentor correspondente para o domínio do utilizador. Informações sobre a existência do utilizador, estado ativado e função, em seguida, ajudam o sistema de autorização determinar se o acesso ao inquilino de destino está autorizado para o usuário nessa sessão. Determinadas ações autorizadas (especificamente, criar utilizador e palavra-passe da reposição) criar uma trilha de auditoria que um administrador de inquilinos, em seguida, utiliza para gerir os esforços de conformidade ou as investigações.

Mover dados entre o seu datacenter no local para o armazenamento do Azure através de uma ligação de Internet não podem sempre ser viáveis devido ao volume de dados, disponibilidade de largura de banda ou outras considerações. O [serviço de importação/exportação de armazenamento do Azure](../../storage/common/storage-import-export-service.md) fornece uma opção baseada em hardware de colocar/obter grandes volumes de dados no armazenamento de Blobs. Ele permite que envie [encriptada com BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) unidades de disco rígido diretamente para um datacenter do Azure onde operadores da nuvem carregar o conteúdo para a conta de armazenamento ou pode transferir os dados do Azure para suas unidades para voltar para. Apenas os discos encriptados são considerados aceites para esse processo (com uma chave do BitLocker gerada pelo serviço durante a configuração da tarefa). A chave do BitLocker é fornecida para o Azure em separado, fornecendo fora de banda de partilha de chave.

Uma vez que os dados em trânsito podem ocorrer em cenários diferentes, também é relevante para saber que utiliza o Microsoft Azure [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/) para isolar o tráfego dos inquilinos uns dos outros, empregando medidas como anfitrião - e ao nível do convidado firewalls, a filtragem de pacotes IP, porta de bloqueio e pontos finais HTTPS. No entanto, a maioria das comunicações internas do Azure, incluindo a infraestrutura de infraestrutura e infraestrutura para o cliente (no local), também é encriptadas. Outro cenário importante é as comunicações entre os datacenters do Azure; Microsoft gerencia redes para garantir que nenhuma VM pode representar ou poder bisbilhotar o endereço IP do outro. O TLS/SSL é utilizado ao aceder ao armazenamento do Azure ou bases de dados SQL ou ao ligar a serviços em nuvem. Neste caso, o administrador do cliente é responsável por obter um certificado TLS/SSL e implantá-lo para sua infra-estrutura de inquilino. A mudança de tráfego dados entre as máquinas virtuais na mesma implementação ou entre inquilinos numa única implementação através da rede Virtual do Microsoft Azure pode ser protegida através de protocolos de comunicação encriptado como HTTPS, SSL/TLS ou outras pessoas.

Dependendo de como respondeu às questões na [determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), deve ser capaz de determinar como pretende proteger os seus dados e como a solução de identidade híbrida pode ajudá-lo com esse processo. A tabela seguinte mostra as opções suportadas pelo Azure que estão disponíveis para cada cenário de proteção de dados.

| Opções de proteção de dados | Em inatividade na cloud | No restante no local | Em trânsito |
| --- | --- | --- | --- |
| Encriptação de Unidade BitLocker |X |X | |
| SQL Server para encriptar as bases de dados |X |X | |
| Encriptação de VM para VM | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Leia [conformidade por funcionalidade](https://azure.microsoft.com/support/trust-center/services/) na [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações que cada serviço do Azure está em conformidade com.
> Uma vez que as opções de proteção de dados utilizam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para esta tarefa. Certifique-se de que vocês estão utilizando todas as opções disponíveis para cada Estado dos dados.
>
>

## <a name="define-content-management-options"></a>Definir as opções de gestão de conteúdos

Uma das vantagens de utilizar o Azure AD para gerir uma infraestrutura de identidade híbrida é que o processo é totalmente transparente do ponto de vista do usuário final. O utilizador tenta aceder a um recurso partilhado, o recurso requer autenticação, o utilizador tem de enviar um pedido de autenticação para o Azure AD para obter o token e aceder ao recurso. Todo este processo ocorre em segundo plano, sem interação do utilizador. 

As organizações que costumam ser preocupação sobre a privacidade de dados requerem a classificação de dados da sua solução. Se a infraestrutura no local atual já estiver a utilizar a classificação de dados, é possível utilizar o Azure AD como o repositório principal para a identidade do utilizador. Uma ferramenta comum, que é utilizada no local para a classificação de dados é denominada [Toolkit de classificação de dados](https://msdn.microsoft.com/library/Hh204743.aspx) para o Windows Server 2012 R2. Essa ferramenta pode ajudar a identificar, classificar e proteger os dados em servidores de ficheiros na sua cloud privada. Também é possível utilizar o [classificação automática de ficheiros](https://technet.microsoft.com/library/hh831672.aspx) no Windows Server 2012 para realizar esta tarefa.

Se a sua organização não tem a classificação de dados no local, mas tem de proteger ficheiros confidenciais sem adicionar novos servidores no local, poderá utilizar Microsoft [serviço Azure Rights Management](https://technet.microsoft.com/library/JJ585026.aspx).  O Azure RMS utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os ficheiros e e-mail e funciona em vários dispositivos — telemóveis, tablets e PCs. Como o Azure RMS é um serviço em nuvem, não é necessário configurar explicitamente fidedignidades com outras organizações para poder partilhar conteúdo protegido com eles. Se estas já possuírem um Office 365 ou um diretório do Azure AD, a colaboração entre organizações automaticamente é suportada. Também pode sincronizar apenas os atributos de diretório do Azure RMS tem de suportar uma identidade comum para as suas contas do Active Directory no local, utilizando os serviços de sincronização de diretório Active Directory do Azure (AAD Sync) ou do Azure AD Connect.

Uma parte essencial do gerenciamento de conteúdo é entender quem está a aceder o recursos, portanto um recurso de Registro em log avançado é importante para a solução de gestão de identidade. O Azure AD fornece o registo mais de 30 dias incluindo:

* Alterações na associação de função (ex: utilizador adicionado à função de Administrador Global)
* Atualizações de credenciais (ex: alterações de palavra-passe)
* Gestão de domínios (ex: verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão de utilizadores (ex: adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

> [!NOTE]
> Leia [gestão de registos de auditoria e de segurança do Microsoft Azure](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais sobre as capacidades de registo no Azure.
> Dependendo de como respondeu às questões na [determinar os requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), deverá conseguir determinar como pretende que o conteúdo sejam geridos na sua solução de identidade híbrida. Embora todas as opções expostas na tabela 6 são capazes de integração com o Azure AD, é importante definir o que é mais apropriada para suas necessidades empresariais.
>
>

| Opções de gestão de conteúdos | Vantagens | Desvantagens |
| --- | --- | --- |
| Centralizado no local (servidor de gestão de direitos do Active Directory) |Controlo total sobre a infraestrutura de servidor responsável por classificar os dados <br> Função incorporado no Windows Server, sem a necessidade de licenciamento extra ou de subscrição <br> Pode ser integrado com o Azure AD num cenário híbrido <br> Suporta as capacidades de gestão (IRM) direitos de informação no Microsoft Online services, como o Exchange Online e SharePoint Online, bem como do Office 365 <br> Suporta produtos de servidor Microsoft no local, como o Exchange Server, SharePoint Server e servidores de ficheiros que executam o Windows Server e a infraestrutura de classificação de ficheiros (FCI). |Manutenção superior (dia com atualizações, configuração e atualizações potenciais), desde IT é o proprietário do servidor <br> Requer uma infraestrutura de servidor no local<br> Não tirar partido das capacidades do Azure nativo |
| Centralizado em cloud (Azure RMS) |Mais fácil de gerenciar em comparação com a solução no local <br> Pode ser integrado com o AD DS num cenário híbrido <br>  Totalmente integrado com o Azure AD <br> Não requer um servidor no local para implementar o serviço <br> Suporta locais produtos de servidor da Microsoft, como o Exchange Server, SharePoint, servidor e os servidores de ficheiros que executam o Windows Server e a classificação de ficheiros, infraestrutura (FCI) <br> IT, pode ter controle total sobre a chave do respetivo inquilino com capacidade BYOK. |Sua organização tem de ter uma subscrição na nuvem que suporte RMS <br> Sua organização tem de ter um diretório do Azure AD para suportar a autenticação de utilizador para RMS |
| Híbrido (integrado do Azure RMS, servidor de gestão de direitos do On-Premises Active Directory) |Este cenário acumula as vantagens de, centralizado no local e na cloud. |Sua organização tem de ter uma subscrição na nuvem que suporte RMS <br> Sua organização tem de ter um diretório do Azure AD para suportar a autenticação de utilizador para RMS, <br> Requer uma ligação entre o serviço cloud do Azure e infraestrutura no local |

## <a name="define-access-control-options"></a>Definir opções de controlo de acesso
Ao tirar partido da autenticação, autorização e funcionalidades de controlo de acesso disponíveis no Azure AD pode ativar a sua empresa utilizar um repositório central de identidade, permitindo que os utilizadores e parceiros para utilizar um único início de sessão (SSO) como mostrado na figura a seguir:

![](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Gestão centralizada e totalmente a integração com outros diretórios

O Azure Active Directory fornece início de sessão único a milhares de aplicações SaaS e aplicativos da web no local. Consulte a [lista de compatibilidades de Federação do Azure Active Directory: fornecedores de identidade de terceiros que podem ser utilizados para implementar o início de sessão único](how-to-connect-fed-compatibility.md) artigo para obter mais detalhes sobre o SSO de terceiros que foram testados pela Microsoft. Esta capacidade permite que a organização implementar uma variedade de cenários B2B enquanto mantém o controlo da gestão de identidades e acessos. No entanto, durante o B2B a criação de processo, é importante compreender o método de autenticação que é usado pelo parceiro e valide se este método é suportado pelo Azure. Atualmente, os métodos seguintes são suportados pelo Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokens
* Certificados

> [!NOTE]
> Leia [protocolos de autenticação do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e seus recursos no Azure.
>
>

Usando o suporte do Azure AD, aplicativos de negócios móveis podem utilizar a mesma experiência de autenticação dos serviços móveis fácil para permitir que os funcionários iniciar sessão nas suas aplicações móveis com as credenciais empresariais do Active Directory. Com esta funcionalidade, do Azure AD é suportado como um fornecedor de identidade nos serviços móveis, juntamente com os outros fornecedores de identidade já suportadas (que incluem Accounts da Microsoft, ID do Facebook, ID do Google e Twitter ID). Se as aplicações no local utilizam as credenciais do utilizador localizada em AD DS da empresa, o acesso a partir de parceiros e os usuários voltarem a partir da cloud deve ser transparente. Pode gerir o controlo de acesso condicional do usuário para aplicativos web (baseados em nuvem), web API, serviços cloud da Microsoft, aplicações de SaaS de terceiros e aplicações de cliente (móvel) nativo e ter os benefícios de segurança, auditoria e relatório todos em um coloca. No entanto, é recomendado para validar a implementação num ambiente de não produção ou com um número limitado de utilizadores.

> [!TIP]
> é importante mencionar que o Azure AD tem diretiva de grupo que tenha de AD DS. Para impor a política para dispositivos, é necessário uma solução de gestão de dispositivos móveis, como [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Assim que o utilizador é autenticado com o Azure AD, é importante avaliar o nível de acesso que o utilizador tem. O nível de acesso que o utilizador tem através de um recurso pode variar. Enquanto o Azure AD pode adicionar uma camada de segurança adicionais, controlar o acesso a alguns recursos, tenha em atenção que o próprio recurso também pode ter sua própria lista de controlo de acesso separadamente, por exemplo, o controlo de acesso para os ficheiros localizados num servidor de ficheiros. A figura a seguir resume os níveis de controlo de acesso que pode ter num cenário híbrido:

![](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Cada interação no diagrama mostrado na figura X representa um cenário de controlo de acesso que pode ser abrangido pelo Azure AD. Abaixo, pode encontrar uma descrição de cada cenário:

1. Acesso condicional a aplicações que estão alojadas no local: Pode utilizar dispositivos registados com políticas de acesso para aplicações que estão configuradas para utilizar o AD FS com o Windows Server 2012 R2.

2. Controlo de acesso ao portal do Azure:  O Azure também permite controlar o acesso ao portal com controlo de acesso baseado em funções (RBAC)). Este método permite que a empresa restringir o número de operações que uma pessoa individual que pode fazer no portal do Azure. Ao utilizar o RBAC para controlar o acesso ao portal, os administradores de TI pode delegar o acesso ao utilizar as seguintes abordagens de gerenciamento de acesso:

   - Atribuição de função baseado no grupo: Pode atribuir acesso a grupos do Azure AD que podem ser sincronizados do Active Directory local. Isso permite que aproveitar os investimentos existentes que sua organização tornou-se em ferramentas e os processos de gestão de grupos. Também pode utilizar a funcionalidade de gestão de grupo delegada do Azure AD Premium.
   - Utilize funções incorporadas no Azure: Pode utilizar três funções — proprietário, Contribuidor e leitor, para garantir que os utilizadores e grupos têm permissão para realizar apenas as tarefas que precisam para realizar seus trabalhos.
   -  Acesso granular aos recursos: Pode atribuir funções a utilizadores e grupos para uma subscrição específica, grupo de recursos ou um recurso do Azure individual como um Web site ou a base de dados. Dessa forma, pode certificar-se de que os utilizadores têm acesso a todos os recursos que precisam e nenhum acesso a recursos que eles não precisam de gerir.

   > [!NOTE]
   > Se estiver a criar aplicações e desejar personalizar o controlo de acesso para os mesmos, também é possível utilizar funções de aplicação do Azure AD para autorização. Veja esta [exemplo de aplicação Web-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sobre como criar a sua aplicação para utilizar esta capacidade.


3. Acesso condicional para aplicações do Office 365 com o Microsoft Intune:  Os administradores de TI podem aprovisionar políticas de dispositivo de acesso condicional para proteger recursos da empresa, e, ao mesmo tempo permitindo que os operadores de informações em dispositivos em conformidade para aceder aos serviços. 
  
4. Acesso condicional para aplicações Saas: [Esta funcionalidade](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) permite-lhe configurar regras de acesso por aplicação multi-factor authentication e a capacidade de bloquear o acesso para os utilizadores não numa rede fidedigna. Pode aplicar as regras de autenticação multifator para todos os utilizadores que estão atribuídos à aplicação, ou apenas para os utilizadores nos grupos de segurança especificados. Os utilizadores podem ser excluídos do requisito de autenticação multifator, se estão a aceder a aplicação a partir de um endereço IP que no dentro da organização de rede.

Uma vez que as opções de controlo de acesso utilizam uma abordagem multicamada, comparação entre essas opções não são aplicáveis para esta tarefa. Certifique-se de que vocês estão utilizando todas as opções disponíveis para cada cenário que requer a controlar o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir as opções de resposta a incidentes
O Azure AD pode ajudá-lo em que a TI da identidade potenciais riscos de segurança no ambiente através da monitorização da atividade do utilizador. IT pode utilizar o acesso do Azure AD e relatórios de utilização ganhar visibilidade sobre a integridade e segurança de diretório da sua organização. Com essas informações, um administrador de TI pode determinar melhor onde podem existir possíveis riscos de segurança para que eles podem planear adequadamente atenuar esses riscos.  [Subscrição do Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que pode permitir que a TI obter essas informações. [Relatórios do Azure AD](../reports-monitoring/overview-reports.md) são classificados da seguinte forma:

* **Relatórios de anomalias**: Conter os eventos de início de sessão que foram detetados a anómalas. O objetivo é conscientizá-lo de tal atividade e permitem-lhe a determinação sobre se um evento está suspeito.
* **Integrado de relatório de aplicação**: Fornece informações sobre como as aplicações na cloud estão a ser utilizadas na sua organização. Azure Active Directory oferece integração com milhares de aplicações na cloud.
* **Relatórios de erros**: Indica os erros que possam ocorrer quando o aprovisionamento de contas em aplicativos externos.
* **Relatórios de utilizador específico**: Apresentar o dispositivo/início de sessão nos dados de atividade para um utilizador específico.
* **Registos de atividades**: Conter um registo de todos os eventos auditados dentro o últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações de atividade de grupo e atividade de registo e reposição de palavra-passe.

> [!TIP]
> Outro relatório que também pode ajudar a equipe de resposta a incidentes, trabalhando num caso é o [utilizador com fuga de credenciais](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) relatório. Este relatório apresenta qualquer correspondência entre a lista de fuga de credenciais e o seu inquilino.
>


Outros importantes relatórios incorporados no Azure AD que pode ser utilizada durante uma investigação de resposta a incidentes e são:

* **Atividade de reposição de palavra-passe**: forneça o administrador com informações sobre como ativamente a reposição de palavra-passe está a ser utilizada na organização.
* **Atividade de registo de reposição de palavra-passe**: fornece informações para o qual utilizadores registarem os seus métodos para a reposição de palavra-passe, e os métodos selecionou.
* **Atividade de grupo**: Fornece um histórico das alterações ao grupo (ex: os utilizadores adicionados ou removidos) que foram iniciados no painel de acesso.

Para além da capacidade de relatórios de núcleos do Azure AD Premium que podem ser usados durante um processo de investigação de resposta a incidentes, TI podem também Aproveite o relatório de auditoria para obter informações como:

* Alterações na associação de função (por exemplo, utilizador adicionado à função de Administrador Global)
* Atualizações de credencial (por exemplo, alterações de palavra-passe)
* Gestão de domínios (por exemplo, verificar um domínio personalizado, remover um domínio)
* Adicionar ou remover aplicações
* Gestão de utilizadores (por exemplo, adicionar, remover, atualizar um utilizador)
* Adicionar ou remover licenças

Uma vez que as opções de resposta a incidentes usar uma abordagem multicamada, comparação entre essas opções não é aplicável para esta tarefa. Certifique-se de que vocês estão utilizando todas as opções disponíveis para cada cenário que requer a utilização de capacidade de relatórios do Azure AD como parte do processo de resposta a incidentes da sua empresa.

## <a name="next-steps"></a>Passos Seguintes
[Determinar as tarefas de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)
