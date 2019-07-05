---
title: 'Azure AD Connect: Conta de serviço do ADSync | Documentos da Microsoft'
description: Este tópico descreve a conta de serviço ADSync e disponibiliza melhores práticas em relação a conta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478722"
---
# <a name="adsync-service-account"></a>Conta de serviço do ADSync
Azure AD Connect instala um serviço no local que orquestra a sincronização entre o Active Directory e Azure Active Directory.  O serviço de sincronização do Microsoft Azure AD Sync (ADSync) é executado num servidor no seu ambiente no local.  As credenciais para o serviço são definidas por predefinição nas instalações Express, mas podem ser personalizadas para satisfazer os seus requisitos de segurança organizacional.  Estas credenciais não são utilizadas para ligar ao Azure Active Directory ou florestas no local.

Escolher o ADSync conta de serviço é uma decisão importante de planejamento para fazer antes de instalar o Azure AD Connect.  Qualquer tentativa de alterar as credenciais, após a instalação irá resultar no serviço a falhar iniciar, perder o acesso à base de dados de sincronização e que não se autenticam com seus diretórios conectados (do Azure e AD DS).  Sem sincronização ocorrerá até que as credenciais originais são restauradas.

## <a name="the-default-adsync-service-account"></a>A conta de serviço ADSync do padrão

Quando executado num servidor membro, o serviço AdSync é executado no contexto de um Virtual serviço conta (VSA).  Devido a uma limitação de produto, é criada uma conta de serviço personalizado quando instalado num controlador de domínio.  Se a conta de serviço de definições Express não cumprir os requisitos de segurança organizacional, implemente o Azure AD Connect, selecionando a opção de personalizar.  Em seguida, escolha a opção de conta de serviço que cumpre os requisitos da sua organização.

>[!NOTE]
>A conta de serviço predefinida quando instalado num controlador de domínio é o formato Domain\AAD_InstallationIdentifier.  A palavra-passe para esta conta é gerada aleatoriamente e apresenta aos desafios significativos para a rotação de recuperação e a palavra-passe.  A Microsoft recomenda a personalizar a conta de serviço durante a instalação inicial num controlador de domínio para utilizar qualquer um dos autónomo ou a conta de serviço gerida de grupo (sMSA / gMSA)

|Localização do Azure AD Connect|Conta de serviço criada|
|-----|-----|
|Servidor membro|NT SERVICE\ADSync|
|Controlador de domínio|Domain\AAD_74dc30c01e80 (ver nota)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço ADSync personalizadas
A Microsoft recomenda a executar o ADSync do serviço no contexto de uma conta de serviço Virtual ou autónomo ou conta de serviço gerida de grupo.  O administrador de domínio também pode optar por criar uma conta de serviço aprovisionada para cumprir os requisitos de segurança organizacionais específicos.   Para personalizar a conta de serviço utilizada durante a instalação, escolha a opção de personalizar a página de definições rápidas abaixo.   As seguintes opções estão disponíveis:

- conta predefinida – Azure AD Connect irá aprovisionar a conta de serviço, conforme descrito acima
- gerido conta de serviço – utilize uma autónoma ou aprovisionado pelo seu administrador MSA de grupo
- conta de domínio – utilização aprovisionada pelo seu administrador de conta de serviço de um domínio

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Alterações de conta de serviço ADSync de diagnóstico
Alterar as credenciais para o serviço ADSync após a instalação irá resultar no serviço a falhar iniciar, perder o acesso à base de dados de sincronização e que não se autenticam com seus diretórios conectados (do Azure e AD DS).  Conceder acesso de base de dados para a nova conta de serviço do ADSync não é suficiente para resolver este problema. Sem sincronização ocorrerá até que as credenciais originais são restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro para o registo de eventos quando não conseguir iniciar.  O conteúdo da mensagem irá variar dependendo se o banco de dados interno (localdb) ou o SQL completo está em utilização.  Seguem-se exemplos das entradas de registo de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

As chaves de encriptação de serviço do AdSync não foi possível encontrar e tem sido recriadas.  Sincronização só ocorrerá até que este problema seja corrigido.

Este problema o Microsoft Azure AD Sync de resolução de problemas chaves de encriptação irão tornar-se inacessível se o AdSync serviço iniciar sessão credenciais forem alteradas.  Se as credenciais foram alteradas, use o aplicativo de serviços para alterar a conta iniciar sessão novamente para o respetivo valor originalmente configurado (por ex. NT SERVICE\AdSync) e reinicie o serviço.  Isso será imediatamente correta a operação de restauro do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para obter mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não conseguiu iniciar porque não foi possível estabelecer uma ligação à base de dados local (localdb).

Resolver problemas do serviço este problema o Microsoft Azure AD Sync, perderá a permissão para aceder ao fornecedor de base de dados local se o AdSync serviço iniciar sessão credenciais forem alteradas.  Se as credenciais foram alteradas usar o aplicativo de serviços para alterar a conta iniciar sessão novamente para o respetivo valor originalmente configurado (por ex. NT SERVICE\AdSync) e reinicie o serviço.  Isso será imediatamente correta a operação de restauro do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para obter mais informações.

Detalhes adicionais, que as seguintes informações de erro foi devolvidas pelo fornecedor:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).