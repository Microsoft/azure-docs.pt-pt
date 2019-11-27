---
title: Diretrizes de segurança para a autenticação multifator do Azure-Azure Active Directory
description: Este documento fornece orientação sobre como usar o Azure MFA com contas do Azure
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
ms.openlocfilehash: 2756d39a93751271c8c7bf2a51108b9fe5b09b1e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208438"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Diretrizes de segurança para usar a autenticação multifator do Azure com contas do Azure AD

A verificação em duas etapas é a escolha preferida para a maioria das organizações que desejam aprimorar seu processo de autenticação. A MFA (autenticação multifator) do Azure ajuda as empresas a atender aos seus requisitos de segurança e conformidade, fornecendo uma experiência de logon simples para seus usuários. Este artigo aborda algumas dicas que você deve considerar ao planejar a adoção do Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implantar o Azure MFA na nuvem

Há duas maneiras de [habilitar o MFA do Azure para todos os usuários](howto-mfa-getstarted.md).

* Comprar licenças para cada usuário (Azure MFA, Azure AD Premium ou Enterprise Mobility + Security)
* Criar um provedor de autenticação multifator e pagar por usuário ou por autenticação

### <a name="licenses"></a>Las

![Aplicar licenças a usuários, habilitar, notificar](./media/multi-factor-authentication-security-best-practices/ems.png)

Se você tiver Azure AD Premium ou Enterprise Mobility + Security licenças, você já tem o Azure MFA. Sua organização não precisa de nada adicional para estender a funcionalidade de verificação em duas etapas para todos os usuários. Você só precisa atribuir uma licença a um usuário e, em seguida, pode ativar o MFA.

Ao configurar a autenticação multifator, considere as seguintes dicas:

* Não crie um provedor de autenticação multifator por autenticação. Se você fizer isso, poderá acabar pagando pelas solicitações de verificação de usuários que já têm licenças.
* Se você não tiver licenças suficientes para todos os seus usuários, poderá criar um provedor de autenticação multifator por usuário para cobrir o restante da sua organização. 
* Azure AD Connect só será necessária se você estiver sincronizando seu ambiente de Active Directory local com um diretório do Azure AD. Se você usar um diretório do AD do Azure que não esteja sincronizado com uma instância local do Active Directory, você não precisará de Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Provedor de autenticação multifator

![Provedor de autenticação multifator](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se você não tiver licenças que incluem o Azure MFA, poderá [criar um provedor de autenticação de MFA](concept-mfa-authprovider.md).

Ao criar o provedor de autenticação, você precisa selecionar um diretório e considerar os seguintes detalhes:

* Você não precisa de um diretório do Azure AD para criar um provedor de autenticação multifator, mas você obtém mais funcionalidade com um. Os recursos a seguir são habilitados quando você associa o provedor de autenticação a um diretório do AD do Azure:
  * Estender a verificação em duas etapas para todos os usuários
  * Ofereça aos seus administradores globais recursos adicionais, como o portal de gerenciamento, saudações personalizadas e relatórios.
* Se você sincronizar seu ambiente de Active Directory local com um diretório do Azure AD, precisará DirSync ou Azure AD Sync. Se você usar um diretório do AD do Azure que não esteja sincronizado com uma instância local do Active Directory, não será necessário DirSync ou Azure AD Sync.
* Escolha o modelo de consumo mais adequado à sua empresa. Depois de selecionar o modelo de uso, você não poderá alterá-lo. Os dois modelos são:
  * Por autenticação: cobra para cada verificação. Use esse modelo se desejar uma verificação em duas etapas para qualquer pessoa que acessa um determinado aplicativo, não para usuários específicos.
  * Por usuário habilitado: cobra você por cada usuário que você habilitar para o Azure MFA. Use esse modelo se você tiver alguns usuários com licenças Azure AD Premium ou Enterprise Mobility Suite e outros sem.

### <a name="supportability"></a>Suportabilidade

Como a maioria dos usuários está acostumado a usar apenas senhas para autenticar, é importante que sua empresa traga consciência para todos os usuários referentes a esse processo. Essa conscientização pode reduzir a probabilidade de que os usuários liguem para o suporte técnico para problemas secundários relacionados à MFA. No entanto, há alguns cenários em que desabilitar temporariamente a MFA é necessária. Use as diretrizes a seguir para entender como lidar com esses cenários:

* Treine sua equipe de suporte técnico para lidar com cenários em que o usuário não pode entrar porque o telefone ou o aplicativo móvel não está recebendo uma notificação ou uma chamada telefônica. O suporte técnico pode [habilitar um bypass](howto-mfa-mfasettings.md#one-time-bypass) único para permitir que um usuário se autentique uma única vez ao "ignorar" a verificação em duas etapas. O bypass é temporário e expira após um número especificado de segundos.
* Considere o [recurso IPs confiáveis](howto-mfa-mfasettings.md#trusted-ips) no Azure MFA como uma maneira de minimizar a verificação em duas etapas. Com esse recurso, os administradores de um locatário gerenciado ou federado podem ignorar a verificação em duas etapas para os usuários que estão entrando da intranet local da empresa. Os recursos estão disponíveis para locatários do Azure AD que têm as licenças do Azure AD Premium, Enterprise Mobility Suite ou autenticação multifator do Azure.

## <a name="best-practices-for-an-on-premises-deployment"></a>Práticas recomendadas para uma implantação local

Se sua empresa decidiu aproveitar sua própria infraestrutura para habilitar a MFA, você precisará [implantar um servidor de autenticação multifator do Azure localmente](howto-mfaserver-deploy.md). Os componentes do servidor MFA são mostrados no diagrama a seguir:

![os componentes padrão do servidor MFA](./media/multi-factor-authentication-security-best-practices/server.png) \*não instalado por padrão \** instalado, mas não habilitado por padrão

O Azure Servidor de Autenticação Multifator pode proteger recursos de nuvem e recursos locais usando a Federação. Você deve ter AD FS e tê-lo federado com seu locatário do Azure AD.
Ao configurar Servidor de Autenticação Multifator, considere os seguintes detalhes:

* Se você estiver protegendo os recursos do Azure AD usando Serviços de Federação do Active Directory (AD FS) (AD FS), a primeira etapa de verificação será executada localmente usando AD FS. O segundo passo é executado no local honrando a afirmação.
* Você não precisa instalar o Azure Servidor de Autenticação Multifator seu servidor de Federação AD FS. No entanto, o adaptador de autenticação multifator para AD FS deve ser instalado em um Windows Server 2012 R2 em execução AD FS. Você pode instalar o servidor em um computador diferente, contanto que seja uma versão com suporte, e instalar o adaptador AD FS separadamente em seu servidor de Federação AD FS. 
* O assistente de instalação do adaptador de autenticação multifator AD FS cria um grupo de segurança chamado PhoneFactor admins no seu Active Directory e, em seguida, adiciona sua conta de serviço do AD FS a esse grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente ao grupo PhoneFactor Admins no seu controlador de domínio.

### <a name="user-portal"></a>Portal de Utilizador

O portal do usuário permite recursos de autoatendimento e fornece um conjunto completo de recursos de administração do usuário. Ele é executado em um site do IIS (servidor de informações da Internet). Use as diretrizes a seguir para configurar este componente:

* Usar o IIS 6 ou superior
* Instalar e registrar o ASP.NET v 2.0.507207
* Verifique se esse servidor pode ser implantado em uma rede de perímetro

### <a name="app-passwords"></a>Senhas de aplicativo

Se sua organização for federada para SSO com o Azure AD e você pretende usar o Azure MFA, lembre-se dos seguintes detalhes:

* A senha do aplicativo é verificada pelo Azure AD e, portanto, ignora a Federação. A Federação é usada somente ao configurar senhas de aplicativo.
* Para usuários federados (SSO), as senhas são armazenadas na ID organizacional. Se o usuário sair da empresa, essas informações precisarão fluir para a ID organizacional usando o DirSync. A desabilitação/exclusão da conta pode levar até três horas para ser sincronizada, o que atrasa a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Nenhum recurso de registro/auditoria de autenticação local está disponível para senhas de aplicativo.
* Determinados designs arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas organizacionais e senhas de aplicativo ao usar a verificação em duas etapas com clientes, dependendo de onde eles se autenticam. Para clientes que se autenticam em uma infraestrutura local, você usaria um nome de usuário e uma senha da organização. Para clientes que se autenticam no Azure AD, você usaria a senha do aplicativo.
* Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativo, selecione a opção **permitir que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** .

## <a name="additional-considerations"></a>Considerações adicionais

Use esta lista para obter considerações adicionais e diretrizes para cada componente implantado no local:

* Configure a Multi-Factor Authentication com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md).
* Defina e configure o Servidor MFA do Azure com [Autenticação RADIUS](howto-mfaserver-dir-radius.md).
* Configure e configure o servidor Azure MFA com a [autenticação do IIS](howto-mfaserver-iis.md).
* Configure e configure o servidor Azure MFA com a [autenticação do Windows](howto-mfaserver-windows.md).
* Configure e configure o servidor Azure MFA com [autenticação LDAP](howto-mfaserver-dir-ldap.md).
* Configure e configure o servidor Azure MFA com [área de trabalho remota gateway e o Azure servidor de autenticação multifator usando o RADIUS](howto-mfaserver-nps-rdg.md).
* Configure e configure a sincronização entre o servidor do Azure MFA e o [Windows server Active Directory](howto-mfaserver-dir-ad.md).
* [Implemente o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](howto-mfaserver-deploy-mobileapp.md).
* [Configuração de VPN avançada com a autenticação multifator do Azure](howto-mfaserver-nps-vpn.md) para dispositivos de VPN Cisco ASA, Citrix Netscaler e Juniper/Pulse seguros usando LDAP ou RADIUS.

## <a name="next-steps"></a>Passos seguintes

Embora este artigo destaque algumas práticas recomendadas para o Azure MFA, há outros recursos que você também pode usar ao planejar sua implantação de MFA. A lista abaixo apresenta alguns artigos importantes que podem ajudá-lo durante esse processo:

* [Relatórios na autenticação multifator do Azure](howto-mfa-reporting.md)
* [A experiência de registro de verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Perguntas frequentes sobre autenticação multifator do Azure](multi-factor-authentication-faq.md)
