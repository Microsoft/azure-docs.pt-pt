---
title: Federação de identidade na Austrália do Azure
description: Orientação sobre como configurar a Federação de identidades nas regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e161e36ee7b403381b65f52a6f416be09025d0a2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570860"
---
# <a name="identity-federation-in-azure-australia"></a>Federação de identidade na Austrália do Azure

O gerenciamento de identidades e a Federação com ofertas de nuvem pública é uma das primeiras etapas mais cruciais para o uso da nuvem. O serviço de Azure Active Directory da Microsoft armazena informações do usuário para habilitar o acesso aos serviços de nuvem e é um pré-requisito para consumir outros serviços do Azure.

Este artigo aborda os principais pontos de design para a implementação de Azure Active Directory, a sincronização de usuários de um domínio Active Directory Domain Services e a implementação de autenticação segura. O foco específico é colocado nas recomendações dos relatórios de segurança da informação do ISM (manual Security Center) e de certificação do Azure na Austrália.

A classificação das informações armazenadas no Azure Active Directory deve informar as decisões sobre como ele foi projetado. O trecho a seguir é fornecido do [relatório de certificação acsc – Microsoft Azure](https://aka.ms/au-irap):

>**Relatório de certificação acsc – Microsoft Azure** O Azure Active Directory (AD do Azure) deve ser configurado com Active Directory serviços de Federação quando as entidades da Comunidade classificarem o uso e o conteúdo de dados de seus Active Directory em protegido. Embora Active Directory dados na classificação de UDLM (inscrições de sinalização não CLASSIFICAda) não exijam Federação, as entidades da Comunidade ainda podem implementar a Federação para atenuar os riscos associados ao serviço que está sendo fornecido de fora da Austrália.

Assim, quais informações são sincronizadas e o mecanismo pelo qual os usuários são autenticados, são as duas principais preocupações abordadas aqui.

## <a name="key-design-considerations"></a>Considerações de design-chave

### <a name="user-synchronisation"></a>Sincronização do usuário

Ao implantar Azure AD Connect, há várias decisões que devem ser feitas sobre os dados que serão sincronizados. O Azure AD Connect se baseia na Microsoft Identity Manager e fornece um conjunto de recursos robusto para [transformar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration) dados entre diretórios.

Os serviços de consultoria da Microsoft podem ser envolvidos para fazer uma avaliação ADRAP do seu Windows Server existente Active Directory. O ADRAP ajuda a determinar quaisquer problemas que precisem ser corrigidos antes da sincronização com o Azure Active Directory. Os contratos do Microsoft Suporte Premier geralmente incluirão esse serviço.

A [ferramenta IDFix](https://docs.microsoft.com/office365/enterprise/install-and-run-idfix) verifica o domínio Active Directory local em busca de problemas antes de sincronizar com o Azure AD. IDFix é uma primeira etapa importante antes de implementar Azure AD Connect. Embora uma verificação IDFix possa identificar uma grande quantidade de problemas, muitos desses problemas podem ser resolvidos rapidamente com scripts ou contratados usando transformações de dados no Azure AD Connect.

O AD do Azure requer que os usuários tenham um domínio de nível superior roteável externamente para habilitar a autenticação. Se o seu domínio tiver um sufixo UPN que não seja roteável externamente, você precisará definir a [ID de entrada alternativa](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname) no AD Connect para o atributo mail do usuário. Em seguida, os usuários entram nos serviços do Azure com seu endereço de email, em vez de entrar em seu domínio.

No entanto, o sufixo UPN em contas de usuário também pode ser alterado usando ferramentas como o PowerShell; Ela pode ter consequências imprevisíveis para outros sistemas conectados e não é mais considerada prática recomendada.

Ao decidir quais atributos sincronizar com Azure Active Directory, é mais seguro pressupor que todos os atributos são necessários. É raro que um diretório contenha dados reais protegidos, no entanto, é recomendável realizar uma auditoria. Se os dados protegidos forem encontrados no diretório, avalie o impacto de omitir ou transformar o atributo. Como um guia útil, há uma lista de atributos que Microsoft Cloud serviços [exigem](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized).

### <a name="authentication"></a>Authentication

É importante entender as opções que estão disponíveis e como elas podem ser usadas para manter os usuários finais seguros.
A Microsoft oferece [três soluções nativas](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) para autenticar usuários em relação ao Azure Active Directory:

* Sincronização de hash de senha-as senhas com hash de Active Directory Domain Services são sincronizadas por Azure AD Connect em Azure Active Directory.
* [Autenticação de passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) -as senhas permanecem dentro do Active Directory Domain Services. Os usuários são autenticados em relação a Active Directory Domain Services por meio de um agente. Nenhuma senha é armazenada no Azure AD.
* [SSO Federado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) -Azure Active Directory é federado com serviços de Federação do Active Directory (AD FS), durante a entrada, o Azure direciona os usuários para serviços de Federação do Active Directory (AD FS) para autenticar. Nenhuma senha é armazenada no Azure AD.

A sincronização de hash de senha pode ser usada em cenários em que os dados oficiais: confidenciais e abaixo estão sendo armazenados no diretório. Os cenários nos quais os dados protegidos estão sendo armazenados exigirão uma das duas opções restantes.

Todas essas três opções dão suporte a [write-back de senha](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback), que o [Guia do consumidor do acsc](https://aka.ms/au-irap) recomenda ser desabilitado. Porém as organizações devem avaliar o risco de desabilitar o Write-back de senha em relação aos ganhos de produtividade e o esforço de suporte reduzido do uso de redefinições de senha de autoatendimento.

#### <a name="pass-through-authentication-pta"></a>Autenticação de passagem (PTA)

A autenticação de passagem foi liberada depois que a avaliação do IRAP foi concluída e, portanto,; deve ser avaliado individualmente para determinar como a solução se ajusta ao perfil de risco de sua organização. A autenticação de passagem é preferida em relação à Federação pela Microsoft devido à postura de segurança aprimorada.

![Autenticação de passagem](media/pta1.png)

A autenticação de passagem apresenta vários fatores de design a serem considerados:

* O agente de autenticação de passagem deve ser capaz de estabelecer conexões de saída para serviços de Microsoft Cloud.
* Instalação de mais de um agente para garantir que o serviço estará altamente disponível. É recomendável implantar pelo menos três agentes e até um máximo de 12 agentes.
* A prática recomendada é evitar instalar o agente diretamente em um Domínio do Active Directory controladores. Por padrão, ao implantar Azure AD Connect com autenticação de passagem, ele instalará o agente no servidor do AD Connect.
* A autenticação de passagem é uma opção de manutenção mais baixa do que a Serviços de Federação do Active Directory (AD FS) porque não requer infraestrutura de servidor dedicada, gerenciamento de certificados ou regras de firewall de entrada.

#### <a name="active-directory-federation-services-adfs"></a>Serviços de Federação do Active Directory (ADFS)

O Serviços de Federação do Active Directory (AD FS) foi incluído na avaliação do IRAP e foi aprovado para uso em ambientes protegidos.

![Federação](media/federated-identity.png)

Serviços de Federação do Active Directory (AD FS) apresenta vários fatores de design a serem considerados:

* Os serviços de Federação exigirão entrada de rede para o tráfego HTTPS da Internet ou para os pontos de extremidade de serviço mínimos da Microsoft.
* Os serviços de Federação usam PKI e certificados, que exigem gerenciamento e renovação contínuos.
* Os serviços de Federação devem ser implantados em servidores dedicados e exigirão a infraestrutura de rede relevante para torná-lo acessível externamente.

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

A seção ISM sobre a autenticação multifator recomenda implementá-la nos seguintes cenários com base em seu perfil de risco:

* Autenticando usuários padrão
* Autenticando contas com privilégios
* Autenticando usuários acesso remoto
* Usuários fazendo ações privilegiadas

O Azure Active Directory fornece a autenticação multifator que pode ser habilitada para todos, ou um subconjunto de usuários (por exemplo, apenas contas com privilégios). A Microsoft também fornece uma solução chamada acesso condicional, que permite um controle mais granular sobre como a autenticação multifator é aplicada (por exemplo, somente quando os usuários entram em intervalos de endereços IP remotos).

A autenticação multifator do Azure dá suporte às seguintes formas de validação aceitáveis do ISM:

* Chamada telefónica
* Mensagem SMS
* Microsoft Authenticator aplicativo
* Tokens de hardware com suporte

Privileged Identity Management, um componente do Azure Active Directory, pode ser usado para impor o uso da autenticação multifator quando os usuários elevam suas permissões para atender à quarta recomendação.

## <a name="next-steps"></a>Passos Seguintes

Examine o artigo sobre [controles de acesso baseado em função e Privileged Identity Management](role-privileged.md).
