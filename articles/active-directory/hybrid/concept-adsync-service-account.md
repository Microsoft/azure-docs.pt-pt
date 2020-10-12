---
title: 'Azure AD Connect: Conta de serviço ADSync / Microsoft Docs'
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
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 875c503a9959565d76d46902b5ecb386995ef1e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86144725"
---
# <a name="adsync-service-account"></a>ADSync service account (conta de serviço do ADSync)
O Azure AD Connect instala um serviço no local que orquestra a sincronização entre o Ative Directory e o Azure Ative Directory.  O serviço de sincronização do Microsoft Azure AD Sync (ADSync) funciona num servidor no seu ambiente no local.  As credenciais para o serviço são definidas por padrão nas instalações do Express, mas podem ser personalizadas de acordo com os seus requisitos de segurança organizacional.  Estas credenciais não são usadas para ligar às suas florestas no local ou ao Diretório Ativo Azure.

Escolher a conta de serviço ADSync é uma decisão importante de planeamento a tomar antes de instalar o Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará no facto de o serviço não ter iniciado, perder o acesso à base de dados de sincronização e não autenticar com os seus diretórios conectados (Azure e AD DS).  Não ocorrerá sincronização até que as credenciais originais sejam restauradas.

## <a name="the-default-adsync-service-account"></a>A conta de serviço ADSync predefinido

Quando executado num servidor de membros, o serviço AdSync funciona no contexto de uma Conta de Serviço Virtual (VSA).  Devido a uma limitação do produto, uma conta de serviço personalizada é criada quando instalada num controlador de domínio.  Se a conta de serviço de definições Express não cumprir os seus requisitos de segurança organizacional, implemente o Azure AD Connect escolhendo a opção Personalizar.  Em seguida, escolha a opção de conta de serviço que satisfaça os requisitos da sua organização.

>[!NOTE]
>A conta de serviço predefinida quando instalada num controlador de domínio é do formulário Domain\AAD_InstallationIdentifier.  A palavra-passe desta conta é gerada aleatoriamente e apresenta desafios significativos para recuperação e rotação de senha.  A Microsoft recomenda personalizar a conta de serviço durante a instalação inicial num controlador de domínio para utilizar uma conta de serviço gerida autónoma ou de grupo (sMSA/ gMSA)

|Localização de conexão Azure AD|Conta de serviço criada|
|-----|-----|
|Servidor de Membros|NT SERVICE\ADSync|
|Controlador de Domínio|Domínio\AAD_74dc30c01e80 (ver nota)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço ADSync personalizadas
A Microsoft recomenda a execução do serviço ADSync no contexto de uma Conta de Serviço Virtual ou de uma Conta de Serviço Gerida autónoma ou do grupo.  O seu administrador de domínio também pode optar por criar uma conta de serviço aprovisionada para satisfazer os seus requisitos específicos de segurança organizacional.   Para personalizar a conta de serviço utilizada durante a instalação, escolha a opção Personalizar na página Definições Expressas abaixo.   Estão disponíveis as seguintes opções:

- conta padrão – Azure AD Connect irá prestar a conta de serviço como descrito acima
- conta de serviço gerido – utilize um MSA autónomo ou grupo MSA aprovisionado pelo seu administrador
- conta de domínio – utilize uma conta de serviço de domínio a ser disponibilizada pelo seu administrador

![Screenshot da página Azure AD Connect Express Settings com botões de opção "Personalizar" ou "Utilizar definições expressas".](media/concept-adsync-service-account/adsync1.png)

![Screenshot da página Azure AD Connect "Instalar componentes necessários" com a opção de utilizar uma conta de serviço gerida existente selecionada.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnóstico de alterações na conta de serviço ADSync
A alteração das credenciais para o serviço ADSync após a instalação resultará na falha do arranque do serviço, na perda de acesso à base de dados de sincronização e na não autenticação com os seus diretórios conectados (Azure e AD DS).  A concessão de acesso à base de dados à nova conta de serviçoSync é insuficiente para recuperar desta edição. Não ocorrerá sincronização até que as credenciais originais sejam restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro no registo do evento quando não for possível iniciar.  O conteúdo da mensagem variará consoante a base de dados incorporada (localdb) ou o SQL completo esteja em uso.  Seguem-se exemplos das entradas de registo de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

As chaves de encriptação de serviço AdSync não foram encontradas e foram recriadas.  A sincronização não ocorrerá até que este problema seja corrigido.

Resolução de problemas neste Problema As teclas de encriptação AD Sync do Microsoft Azure tornar-se-ão inacessíveis se as credenciais do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, utilize o pedido Serviço para alterar a conta Log On de volta ao seu valor originalmente configurado (ex. NT SERVICE\AdSync) e reiniciar o serviço.  Isto irá restaurar imediatamente o correto funcionamento do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não foi possível iniciar porque não foi possível estabelecer uma ligação à base de dados local (localdb).

Resolução de problemas neste problema O serviço Microsoft Azure AD Sync perderá permissão para aceder ao fornecedor de bases de dados local se as credenciais do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, utilize o pedido serviço para alterar a conta Log On de volta ao seu valor originalmente configurado (ex. NT SERVICE\AdSync) e reiniciar o serviço.  Isto irá restaurar imediatamente o correto funcionamento do serviço AdSync.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para mais informações.

Detalhes Adicionais As seguintes informações de erro foram devolvidas pelo fornecedor:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
