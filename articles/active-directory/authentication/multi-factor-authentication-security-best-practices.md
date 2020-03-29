---
title: Orientação de segurança para autenticação de multi-factores Azure - Diretório Ativo Azure
description: Este documento fornece orientação em torno da utilização de Azure MFA com contas Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846832"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Orientação de segurança para a utilização da autenticação de vários fatores Azure com contas AD Azure

A verificação em duas etapas é a escolha preferida para a maioria das organizações que querem melhorar o seu processo de autenticação. A Autenticação Multi-Factor (MFA) do Azure ajuda as empresas a cumpriros os seus requisitos de segurança e conformidade, ao mesmo tempo que proporciona uma experiência de entrada simples para os seus utilizadores. Este artigo cobre algumas dicas que deve considerar ao planear a adoção de MFA Azure.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implementar MFA Azure na nuvem

Existem duas formas de ativar o [Azure MFA para todos os seus utilizadores.](howto-mfa-getstarted.md)

* Comprar licenças para cada utilizador (Azure MFA, Azure AD Premium ou Enterprise Mobility + Security)
* Criar um Fornecedor Auth Multifactor e pagar por utilizador ou por autenticação

### <a name="licenses"></a>Licenças

![Aplicar licenças aos utilizadores, ativar, notificar](./media/multi-factor-authentication-security-best-practices/ems.png)

Se tem licenças Azure AD Premium ou Enterprise Mobility + Security, já tem Licenças Azure MFA. A sua organização não precisa de nada adicional para alargar a capacidade de verificação em duas etapas a todos os utilizadores. Só precisa de atribuir uma licença a um utilizador e depois pode ligar o MFA.

Ao configurar a autenticação multi-factor, considere as seguintes dicas:

* Não crie um Fornecedor Multi-Factor Auth de autenticação. Se o fizer, poderá acabar por pagar pedidos de verificação de utilizadores que já tenham licenças.
* Se não tiver licenças suficientes para todos os seus utilizadores, pode criar um Fornecedor Multifactor Auth por utilizador para cobrir o resto da sua organização. 
* O Azure AD Connect só é necessário se estiver a sincronizar o seu ambiente de Diretório Ativo no local com um diretório Azure AD. Se utilizar um diretório Azure AD que não seja sincronizado com uma instância no local de Diretório Ativo, não precisa de Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Fornecedor auth multi-factor

![Fornecedor de autenticação de vários fatores](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se não tiver licenças que incluam O MFA Azure, então pode [criar um MFA Auth Provider](concept-mfa-authprovider.md).

Ao criar o Fornecedor Auth, é necessário selecionar um diretório e considerar os seguintes detalhes:

* Não é necessário um diretório Azure AD para criar um Fornecedor Auth Multifactor, mas obtém-se mais funcionalidade com um. As seguintes funcionalidades são ativadas quando associa o Fornecedor Auth a um diretório Azure AD:
  * Alargar a verificação em duas etapas a todos os seus utilizadores
  * Ofereça aos seus administradores globais funcionalidades adicionais, tais como o portal de gestão, saudações personalizadas e relatórios.
* Se sincronizar o seu ambiente de Diretório Ativo no local com um diretório Azure AD, precisa de DirSync ou Azure AD Sync. Se utilizar um diretório Azure AD que não seja sincronizado com uma instância no local de Diretório Ativo, não precisa de DirSync ou Azure AD Sync.
* Escolha o modelo de consumo que melhor se adapte ao seu negócio. Uma vez selecionado o modelo de utilização, não pode mudá-lo. Os dois modelos são:
  * Por autenticação: cobra-lhe por cada verificação. Utilize este modelo se quiser uma verificação em duas etapas para qualquer pessoa que aceda a uma determinada aplicação, e não para utilizadores específicos.
  * Por utilizador habilitado: cobra-lhe por cada utilizador que ativa para o Azure MFA. Utilize este modelo se tiver alguns utilizadores com licenças Azure AD Premium ou Enterprise Mobility Suite, e alguns sem.

### <a name="supportability"></a>Suportabilidade

Uma vez que a maioria dos utilizadores está habituada a usar apenas senhas para autenticar, é importante que a sua empresa traga conhecimento a todos os utilizadores sobre este processo. Esta consciência pode reduzir a probabilidade de os utilizadores ligarem para o seu balcão de ajuda para problemas menores relacionados com o MFA. No entanto, existem alguns cenários em que é necessário desativar temporariamente o MFA. Utilize as seguintes orientações para entender como lidar com esses cenários:

* Treine a sua equipa de suporte técnico para lidar com cenários em que o utilizador não possa iniciar sessão porque a aplicação móvel ou o telemóvel não está a receber uma notificação ou chamada telefónica. O suporte técnico pode [permitir um bypass único](howto-mfa-mfasettings.md#one-time-bypass) para permitir que um utilizador autentique uma única vez através da verificação em duas etapas "contornando". O bypass é temporário e expira após um número especificado de segundos.
* Considere a capacidade de [IPs fidedigno](howto-mfa-mfasettings.md#trusted-ips) em Azure MFA como uma forma de minimizar a verificação em duas etapas. Com esta funcionalidade, os administradores de um inquilino gerido ou federado podem contornar a verificação em duas etapas para os utilizadores que estão a iniciar sessão a partir da intranet local da empresa. As funcionalidades estão disponíveis para inquilinos da Azure AD que possuem licenças Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Boas Práticas para uma implantação no local

Se a sua empresa decidiu alavancar a sua própria infraestrutura para permitir o MFA, então precisa de implantar um Servidor de [Autenticação Multi-Factor Azure no local.](howto-mfaserver-deploy.md) Os componentes do Servidor MFA são mostrados no seguinte diagrama:

![Os componentes](./media/multi-factor-authentication-security-best-practices/server.png) \*padrão do Servidor MFA \*Não instalados por padrão *Instalados mas não ativados por predefinição

O Azure Multi-Factor Authentication Server pode garantir recursos na nuvem e recursos no local utilizando a federação. Você deve ter AD FS e federado com o seu inquilino Azure AD.
Ao configurar o Servidor de Autenticação multi-factor, considere os seguintes detalhes:

* Se estiver a assegurar os recursos da Azure AD utilizando os Serviços da Federação de Diretórios Ativos (AD FS), então o primeiro passo de verificação é realizado no local utilizando AD FS. O segundo passo é executado no local honrando a afirmação.
* Não tem de instalar o Servidor de Autenticação Multi-Factor Azure do seu servidor da federação AD FS. No entanto, o adaptador de autenticação multi-factor para AD FS deve ser instalado num Windows Server 2012 R2 executando AD FS. Pode instalar o servidor num computador diferente, desde que seja uma versão suportada e instalar o adaptador AD FS separadamente no seu servidor da federação AD FS. 
* O assistente de instalação de adaptação AD FS de autenticação multi-factor cria um grupo de segurança chamado PhoneFactor Admins no seu Diretório Ativo e, em seguida, adiciona a sua conta de serviço AD FS a este grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente ao grupo PhoneFactor Admins no seu controlador de domínio.

### <a name="user-portal"></a>Portal de Utilizador

O portal do utilizador permite capacidades de self-service e fornece um conjunto completo de capacidades de administração do utilizador. Funciona num site do Internet Information Server (IIS). Utilize as seguintes diretrizes para configurar este componente:

* Use iIS 6 ou superior
* Instalar e registar ASP.NET v2.0.507207
* Certifique-se de que este servidor pode ser implantado numa rede de perímetro

### <a name="app-passwords"></a>Palavras-passe para aplicações

Se a sua organização for federada para SSO com AD Azure e você vai usar O MFA Azure, então esteja ciente dos seguintes detalhes:

* A palavra-passe da aplicação é verificada pela Azure AD e, portanto, contorna a federação. A Federação só é utilizada na configuração de senhas de aplicação.
* Para utilizadores federados (SSO), as palavras-passe são armazenadas no ID organizacional. Se o utilizador deixar a empresa, essa informação tem de fluir para id organizacional utilizando o DirSync. A desativação/eliminação da conta pode demorar até três horas a sincronizar, o que atrasa a desativação/eliminação de senhas de aplicação em AD Azure.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Não existe nenhuma capacidade de autenticação/auditoria de autenticação no local para senhas de aplicação.
* Certos desenhos arquitetónicos avançados podem exigir a utilização de uma combinação de nomede utilizador organizacional e palavras-passe e palavras-passe de aplicações quando se utiliza uma verificação em duas etapas com os clientes, dependendo do local onde autenticam. Para clientes que autenticam contra uma infraestrutura no local, utilizaria um nome de utilizador organizacional e uma palavra-passe. Para clientes que autenticam contra o Azure AD, utilizaria a palavra-passe da aplicação.
* Por padrão, os utilizadores não podem criar senhas de aplicação. Caso seja necessário permitir que os utilizadores criem palavras-passe de apps, selecione os **utilizadores do Permitir em criar palavras-passe de aplicações para iniciar sessão na opção de aplicações não-navegadoras.**

## <a name="additional-considerations"></a>Considerações adicionais

Utilize esta lista para considerações e orientações adicionais para cada componente que seja implantado no local:

* Configure a Multi-Factor Authentication com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md).
* Defina e configure o Servidor MFA do Azure com [Autenticação RADIUS](howto-mfaserver-dir-radius.md).
* Configurar e configurar o Servidor Azure MFA com [autenticação IIS](howto-mfaserver-iis.md).
* Configurar e configurar o Servidor Azure MFA com [autenticação do Windows](howto-mfaserver-windows.md).
* Configurar e configurar o Servidor Azure MFA com [autenticação LDAP](howto-mfaserver-dir-ldap.md).
* Configure e configure o Servidor Azure MFA com gateway de ambiente de trabalho remoto e servidor de [autenticação multi-factor azure utilizando RADIUS](howto-mfaserver-nps-rdg.md).
* Configure e configure a sincronização entre o Servidor Azure MFA e o [Diretório Ativo do Servidor do Windows](howto-mfaserver-dir-ad.md).
* Implemente o Serviço Web web do servidor de [autenticação multi-factor Azure](howto-mfaserver-deploy-mobileapp.md).
* [Configuração AVANÇADA vpn com autenticação de multi-factor azure](howto-mfaserver-nps-vpn.md) para Cisco ASA, Citrix Netscaler e eletrodomésticos Juniper/Pulse Secure VPN utilizando LDAP ou RADIUS.

## <a name="next-steps"></a>Passos seguintes

Embora este artigo destaque algumas boas práticas para o Azure MFA, existem outros recursos que também pode utilizar enquanto planeia a sua implementação de MFA. A lista abaixo tem alguns artigos-chave que podem ajudá-lo durante este processo:

* [Relatórios na autenticação multi-factor azure](howto-mfa-reporting.md)
* [A experiência de registo de verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md)
* [FAQ de autenticação multi-factor Azure](multi-factor-authentication-faq.md)
