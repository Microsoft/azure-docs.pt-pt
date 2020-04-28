---
title: 'Azure AD Connect: Conta de serviço ADSync Microsoft Docs'
description: Este tópico descreve a conta de serviço ADSync e fornece as melhores práticas em relação à conta.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67478722"
---
# <a name="adsync-service-account"></a>ADSync service account (conta de serviço do ADSync)
O Azure AD Connect instala um serviço no local que orquestra a sincronização entre o Ative Directory e o Azure Ative Directory.  O serviço de sincronização Microsoft Azure AD Sync (ADSync) funciona num servidor no seu ambiente no local.  As credenciais para o serviço são definidas por padrão nas instalações do Expresso, mas podem ser personalizadas para satisfazer os seus requisitos de segurança organizacional.  Estas credenciais não são usadas para se conectarem às suas florestas no local ou ao Diretório Ativo Azure.

Escolher a conta de serviço ADSync é uma importante decisão de planeamento a tomar antes da instalação do Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará na não arranque do serviço, perdendo acesso à base de dados de sincronização e não autenticando com os seus diretórios conectados (Azure e AD DS).  Não ocorrerá sincronização até que as credenciais originais sejam restauradas.

## <a name="the-default-adsync-service-account"></a>A conta de serviço ADSync padrão

Quando executado num servidor membro, o serviço AdSync funciona no contexto de uma Conta de Serviço Virtual (VSA).  Devido a uma limitação do produto, uma conta de serviço personalizada é criada quando instalada num controlador de domínio.  Se a conta de serviço de configurações Express não cumprir os seus requisitos de segurança organizacional, implemente o Azure AD Connect escolhendo a opção Personalizar.  Em seguida, escolha a opção de conta de serviço que satisfaz os requisitos da sua organização.

>[!NOTE]
>A conta de serviço predefinida quando instalada num controlador de domínio é do formulário Domínio\AAD_InstallationIdentifier.  A palavra-passe para esta conta é gerada aleatoriamente e apresenta desafios significativos para a recuperação e rotação de passwords.  A Microsoft recomenda personalizar a conta de serviço durante a instalação inicial num controlador de domínio para utilizar uma conta de serviço autónoma ou gerida por grupo (sMSA/ gMSA)

|Localização azure AD Connect|Conta de serviço criada|
|-----|-----|
|Servidor membro|SERVIÇO NT\Adsync|
|Controlador de Domínio|Domínio\AAD_74dc30c01e80 (ver nota)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço ADSync personalizadas
A Microsoft recomenda executar o serviço ADSync no contexto de uma Conta de Serviço Virtual ou de uma Conta de Serviço Gerida autónoma ou em grupo.  O seu administrador de domínio também pode optar por criar uma conta de serviço aprovisionada para satisfazer os seus requisitos específicos de segurança organizacional.   Para personalizar a conta de serviço utilizada durante a instalação, escolha a opção Personalizar na página Definições express abaixo.   Estão disponíveis as seguintes opções:

- conta por predefinição – O Azure AD Connect fornecerá a conta de serviço como descrito acima
- conta de serviço gerida - utilize um MSA autónomo ou de grupo provisionado pelo seu administrador
- conta de domínio - utilize uma conta de serviço de domínio provisionada pelo seu administrador

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnóstico de alterações na conta de serviço ADSync
A alteração das credenciais para o serviço ADSync após a instalação resultará na não arranque do serviço, perdendo acesso à base de dados de sincronização e não autenticando com os seus diretórios conectados (Azure e AD DS).  A concessão de acesso à base de dados à nova conta de serviço ADSync é insuficiente para recuperar deste problema. Não ocorrerá sincronização até que as credenciais originais sejam restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro no registo do evento quando não conseguir arrancar.  O conteúdo da mensagem variará consoante a base de dados incorporada (localdb) ou o SQL completo estiver em uso.  Seguem-se exemplos das entradas de registo de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

As chaves de encriptação do serviço AdSync não puderam ser encontradas e foram recriadas.  A sincronização não ocorrerá até que esta questão seja corrigida.

Resolução de problemas deste problema As teclas de encriptação Microsoft Azure AD Sync tornar-se-ão inacessíveis se as credenciais de log on do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, utilize a aplicação Serviços para alterar a conta Log On de volta para o seu valor originalmente configurado (ex. SERVIÇO NT\AdSync) e reiniciar o serviço.  Isto restabelecerá imediatamente o correto funcionamento do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não pôde arrancar porque não foi possível estabelecer uma ligação à base de dados local (localdb).

Resolução de problemas deste problema O serviço Microsoft Azure AD Sync perderá permissão para aceder ao fornecedor de bases de dados local se as credenciais de log on do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, utilize a aplicação Serviços para alterar a conta Log On de volta ao seu valor originalmente configurado (ex. SERVIÇO NT\AdSync) e reiniciar o serviço.  Isto restabelecerá imediatamente o correto funcionamento do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para mais informações.

Detalhes adicionais As seguintes informações de erro foram devolvidas pelo fornecedor:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).