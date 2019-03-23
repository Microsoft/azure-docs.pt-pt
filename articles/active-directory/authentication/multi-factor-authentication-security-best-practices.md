---
title: Orientações de segurança para o Azure multi-factor Authentication - Azure Active Directory
description: Este documento fornece orientações sobre como utilizar o MFA do Azure com contas do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436b7899b1a9d4f9cab1ca2581ff9b5b162de8ac
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368216"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Orientações de segurança para o Azure multi-factor Authentication com contas do Azure AD

Verificação de dois passos é a opção preferencial para a maioria das organizações que deseja aprimorar seu processo de autenticação. O Azure multi-factor Authentication (MFA) ajuda as empresas a cumprir os respetivos requisitos de segurança e conformidade ao mesmo tempo, uma experiência de início de sessão simples para os seus utilizadores. Este artigo aborda algumas dicas que deve considerar quando planear a adoção do MFA do Azure.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implementar o MFA do Azure na cloud

Existem duas formas de [ativar o MFA do Azure para todos os seus utilizadores](howto-mfa-getstarted.md).

* Comprar licenças para cada utilizador (o MFA do Azure, Azure AD Premium ou Enterprise Mobility + Security)
* Criar um fornecedor do multi-factor Auth e pagar por usuário ou por autenticação

### <a name="licenses"></a>Licenças

![Aplicar as licenças aos utilizadores, ativar, notificar](./media/multi-factor-authentication-security-best-practices/ems.png)

Se tiver o Azure AD Premium ou Enterprise Mobility + licenças de segurança, já tem MFA do Azure. Sua organização não precise de nada adicional para expandir a capacidade de verificação de dois passos para todos os utilizadores. Só tem de atribuir uma licença a um utilizador e, em seguida, pode ativar o MFA.

Quando configurar o multi-factor Authentication, considere as sugestões seguintes:

* Não crie um fornecedor do multi-factor Auth por autenticação. Se o fizer, poderia acabar por pagar para pedidos de verificação de utilizadores que já tem licenças.
* Se não tiver licenças suficientes para todos os seus utilizadores, pode criar um fornecedor de autenticação do multi-factor de por utilizador para cobrir o resto da sua organização. 
* Azure AD Connect é apenas necessário se estiver a sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD. Se utilizar um diretório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não é necessário o Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Fornecedor do multi-factor Auth

![Fornecedor de multi-factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se não tiver licenças que incluem o MFA do Azure, em seguida, pode [criar um fornecedor de autenticação de MFA](concept-mfa-authprovider.md).

Ao criar o fornecedor de autenticação, tem de selecionar um diretório e considere os seguintes detalhes:

* Não é necessário um diretório do Azure AD para criar um fornecedor do multi-factor Auth, mas obtém mais funcionalidade com um. As seguintes funcionalidades estão ativadas quando associa o fornecedor de autenticação com um diretório do Azure AD:
  * Expandir a verificação de dois passos para todos os seus utilizadores
  * Oferecem funcionalidades adicionais, como o portal de gestão, saudações personalizadas e os relatórios dos administradores globais.
* Se sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD, terá de DirSync ou o AAD Sync. Se utilizar um diretório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não é necessário DirSync ou o AAD Sync.
* Selecione o modelo de consumo que melhor se adequa aos seus negócios. Depois de selecionar o modelo de uso, não é possível alterá-lo. Os dois modelos são:
  * Por autenticação: os custos de para cada verificação. Use esse modelo se pretender que a verificação de dois passos para qualquer pessoa que acede a uma determinada aplicação, não para utilizadores específicos.
  * Por utilizador ativado: os custos de para cada utilizador que ativar para a MFA do Azure. Use esse modelo se tiver alguns utilizadores com o Azure AD Premium ou licenças do Enterprise Mobility Suite e outros, sem.

### <a name="supportability"></a>Suportabilidade

Uma vez que a maioria dos usuários estão acostumados a utilizar apenas as palavras-passe para autenticar, é importante que sua empresa traz reconhecimento a todos os utilizadores em relação a esse processo. Esta deteção pode reduzir a probabilidade de que os utilizadores, ligue o suporte técnico para problemas secundários relacionados com a MFA. No entanto, existem alguns cenários em que é necessário desativar temporariamente a MFA. Utilize as diretrizes seguintes para compreender como lidar com esses cenários:

* Treine sua equipe de suporte técnico para manipular os cenários em que o utilizador não pode iniciar sessão uma vez que a aplicação móvel ou por telefone não está a receber uma notificação ou uma chamada telefónica. Suporte técnico pode [ativar uma omissão de uso individual](howto-mfa-mfasettings.md#one-time-bypass) para permitir que um utilizador seja autenticado uma vez por "Ignorar" verificação de dois passos. A omissão é temporária e expira após um número de segundos especificado.
* Considere a [capacidade de IPs fidedignos](howto-mfa-mfasettings.md#trusted-ips) na MFA do Azure como uma forma para minimizar a verificação de dois passos. Com esta funcionalidade, os administradores de um inquilino gerido ou Federado podem ignorar a verificação de dois passos para os utilizadores que são iniciar sessão a partir da intranet local da empresa. As funcionalidades estão disponíveis para inquilinos do Azure AD que têm licenças do Azure AD Premium, o Enterprise Mobility Suite ou o Azure multi-factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Melhores práticas para uma implementação no local

Se a empresa decidiu aproveitar sua própria infraestrutura para ativar a MFA, então precisa [implementar um servidor de autenticação multi-factor do Azure no local](howto-mfaserver-deploy.md). Os componentes de servidor MFA são apresentados no diagrama seguinte:

![Os componentes padrão do servidor MFA](./media/multi-factor-authentication-security-best-practices/server.png) \*não instalado por predefinição \** instalado mas não ativado por predefinição

O Azure multi-factor Authentication Server pode obter as cloud recursos de recursos e no local utilizando Federação. Tem de ter o AD FS e fazer com que ele federadas com o inquilino do Azure AD.
Quando configurar o servidor multi-factor Authentication, considere os seguintes detalhes:

* Se estiver a proteger recursos do Azure AD com serviços de Federação do Active Directory (AD FS), em seguida, o primeiro passo de verificação é executado no local através do AD FS. O segundo passo é executado no local honrando a afirmação.
* Não tem de instalar o servidor multi-factor Authentication do Azure seu servidor de Federação do AD FS. No entanto, o adaptador do multi-factor Authentication para AD FS tem de ser instalado num Windows Server 2012 R2 a executar o AD FS. Pode instalar o servidor num computador diferente, desde que é uma versão suportada e instalar o adaptador AD FS separadamente no seu servidor de Federação do AD FS. 
* O Assistente de instalação do adaptador do multi-factor Authentication. o AD FS cria um grupo de segurança chamado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS a este grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente ao grupo PhoneFactor Admins no seu controlador de domínio.

### <a name="user-portal"></a>Portal de Utilizador

O portal de utilizador permite capacidades self-service e fornece um conjunto completo de capacidades de administração do utilizador. Ele é executado num site do servidor de informação Internet (IIS). Utilize as diretrizes seguintes para configurar este componente:

* Usar o IIS 6 ou superior
* Instalar e registar o ASP.NET v2.0.507207
* Certifique-se de que este servidor de pode ser implementado numa rede de perímetro

### <a name="app-passwords"></a>Palavras-passe de Aplicação

Se sua organização estiver federada para SSO com o Azure AD e vai estar a utilizar o MFA do Azure, em seguida, tenha em atenção os seguintes detalhes:

* A palavra-passe de aplicação é verificada pelo Azure AD e, por conseguinte, ignora o Federação. A Federação apenas é utilizada quando configurar as palavras-passe de aplicação.
* Para os utilizadores federados (SSO), as senhas são armazenadas no ID organizacional. Se o utilizador sai da empresa, tem de fluir para o ID organizacional utilizando DirSync essas informações. A desativação/eliminação de conta pode demorar até três horas para sincronização, o que atrasa a desativação/eliminação de palavras-passe de aplicação no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Sem capacidade de log/auditoria da autenticação no local está disponível para as palavras-passe de aplicação.
* Determinados designs de arquiteturais avançadas podem requerer a utilização de uma combinação de nome de utilizador organizacional e palavras-passe e palavras-passe de aplicação ao utilizar a verificação de dois passos com clientes, dependendo de onde se autenticar. Para clientes de autenticação em relação a uma infraestrutura no local, usaria um nome de utilizador organizacional e a palavra-passe. Para clientes que se autenticar no Azure AD, tem de utilizar a palavra-passe de aplicação.
* Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação. Se tiver de permitir aos utilizadores criar palavras-passe aplicação, selecione o **permitir que os utilizadores criem palavras-passe de aplicação para iniciar sessão em aplicações não baseadas no browser** opção.

## <a name="additional-considerations"></a>Considerações adicionais

Utilize esta lista para obter considerações adicionais e orientações para cada componente que é implementado no local:

* Configure a Multi-Factor Authentication com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md).
* Defina e configure o Servidor MFA do Azure com [Autenticação RADIUS](howto-mfaserver-dir-radius.md).
* Definir e configurar o servidor de MFA do Azure com [autenticação do IIS](howto-mfaserver-iis.md).
* Definir e configurar o servidor de MFA do Azure com [autenticação do Windows](howto-mfaserver-windows.md).
* Definir e configurar o servidor de MFA do Azure com [autenticação LDAP](howto-mfaserver-dir-ldap.md).
* Definir e configurar o servidor de MFA do Azure com [Gateway de ambiente de trabalho remoto e servidor de autenticação do multi-factor do Azure com o RADIUS](howto-mfaserver-nps-rdg.md).
* Definir e configurar a sincronização entre o servidor de MFA do Azure e [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
* [Implemente o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](howto-mfaserver-deploy-mobileapp.md).
* [Avançadas de configuração de VPN com multi-factor Authentication](howto-mfaserver-nps-vpn.md) para ASA da Cisco, Citrix Netscaler, Juniper/Pulse Secure VPN e aplicações através de LDAP ou RADIUS.

## <a name="next-steps"></a>Passos Seguintes

Embora este artigo destaca algumas das melhores práticas para a MFA do Azure, existem outros recursos que também pode utilizar ao planear a implementação de MFA. A lista abaixo tem alguns artigos principais que podem ajudá-lo durante este processo:

* [Relatórios no Azure multi-factor Authentication](howto-mfa-reporting.md)
* [A experiência de registo de verificação de dois passos](../user-help/multi-factor-authentication-end-user-first-time.md)
* [FAQ do multi-factor Authentication](multi-factor-authentication-faq.md)
