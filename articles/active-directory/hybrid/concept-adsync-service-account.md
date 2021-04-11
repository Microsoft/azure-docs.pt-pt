---
title: 'Azure AD Connect: AdSync conta de serviço | Microsoft Docs'
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
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca99a997d621bfd2455e909b36b6802775b20ac2
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074612"
---
# <a name="adsync-service-account"></a>ADSync service account (conta de serviço do ADSync)
O Azure AD Connect instala um serviço no local que orquestra a sincronização entre o Ative Directory e o Azure Ative Directory.  O serviço de sincronização do Microsoft Azure AD Sync (ADSync) funciona num servidor no seu ambiente no local.  As credenciais para o serviço são definidas por padrão nas instalações do Express, mas podem ser personalizadas de acordo com os seus requisitos de segurança organizacional.  Estas credenciais não são usadas para ligar às suas florestas no local ou ao Diretório Ativo Azure.

Escolher a conta de serviço ADSync é uma decisão importante de planeamento a tomar antes de instalar o Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará no facto de o serviço não ter iniciado, perder o acesso à base de dados de sincronização e não autenticar com os seus diretórios conectados (Azure e AD DS).  Não ocorrerá sincronização até que as credenciais originais sejam restauradas.

O serviço de sincronização pode ser executado em diferentes contas. Pode ser executado numa Conta de Serviço Virtual (VSA), numa Conta de Serviço Gerido (gMSA/sMSA), ou numa Conta de Utilizador regular. As opções suportadas foram alteradas com o lançamento de abril de 2017 e o lançamento de março de 2021 do Azure AD Connect quando faz uma nova instalação. Se atualizar a partir de uma versão anterior do Azure AD Connect, estas opções adicionais não estão disponíveis. 


|Tipo de conta|Opção de instalação|Description| 
|-----|------|-----|
|Conta de Serviço Virtual|Expresso e personalizado, abril de 2017 e mais tarde| Uma Conta de Serviço Virtual é utilizada para todas as instalações expressas, com exceção das instalações num Controlador de Domínio. Ao utilizar a instalação personalizada, é a opção predefinida, a menos que seja utilizada outra opção.| 
|Conta de Serviço Gerida|Personalizado, abril de 2017 e mais tarde|Se utilizar um Servidor SQL remoto, recomendamos a utilização de uma conta de serviço gerida pelo grupo. |
|Conta de Serviço Gerida|Expresso e personalizado, 2021 março e mais tarde|Uma conta de serviço gerida autónoma pré-fixado com ADSyncMSA_ é criada durante a instalação para instalações expressas quando instalada num Controlador de Domínio. Ao utilizar a instalação personalizada, é a opção predefinida, a menos que seja utilizada outra opção.|
|Conta de Utilizador|Expresso e personalizado, abril de 2017 a 2021 março|Uma conta de utilizador pré-fixa com AAD_ é criada durante a instalação para instalações expressas quando instalada num Controlador de Domínio. Ao utilizar a instalação personalizada, é a opção predefinida, a menos que seja utilizada outra opção.|
|Conta de Utilizador|Expresso e personalizado, março de 2017 e mais cedo|Uma Conta de Utilizador pré-fixa com AAD_ é criada durante a instalação para instalações expressas. Ao utilizar a instalação personalizada, pode especificar outra conta.| 

>[!IMPORTANT]
> Se utilizar o Connect com uma construção a partir de março de 2017 ou mais cedo, então não deverá redefinir a palavra-passe na conta de serviço, uma vez que o Windows destrói as chaves de encriptação por razões de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se fizer o upgrade para uma construção a partir de abril de 2017 ou posterior, então é suportado para alterar a palavra-passe na conta de serviço, mas não pode alterar a conta utilizada. 

> [!IMPORTANT]
> Só pode definir a conta de serviço na primeira instalação. Não é suportado para alterar a conta de serviço depois de concluída a instalação. Se precisar de alterar a palavra-passe da conta de serviço, esta é suportada e as instruções podem ser encontradas [aqui](how-to-connect-sync-change-serviceacct-pass.md).

Segue-se uma tabela das opções de incumprimento, recomendadas e suportadas para a conta de serviço de sincronização. 

Legenda: 

- **O negrito** indica a opção predefinição e, na maioria dos casos, a opção recomendada. 
- *Itálico* indica a opção recomendada quando não é a opção predefinitiva. 
- Não-arrojado - Opção suportada 
- Conta local - Conta de utilizador local no servidor 
- Conta de domínio - Conta de utilizador de domínio 
- sMSA - [conta de Serviço Gerido Autónomo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA - [conta de serviço gerido do grupo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

|Tipo de máquina |**Expresso </br> LocalDB**|**LocalDB/LocalSQL </br> Costume**|**Personalizado SQL remoto </br>**|
|-----|-----|-----|-----|
|**máquina unida ao domínio**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> Conta local</br> Conta do domínio| *gMSA* </br>Conta do domínio|
|Controlador de Domínio| **sMSA**|**sMSA** </br>*gMSA*</br> Conta do domínio|*gMSA*</br>Conta do domínio| 

## <a name="virtual-service-account"></a>Conta de Serviço Virtual 

Uma Conta de Serviço Virtual é um tipo especial de conta local gerida que não tem uma senha e é gerida automaticamente pelo Windows. 

 ![Conta de serviço virtual](media/concept-adsync-service-account/account-1.png)

A Conta de Serviço Virtual destina-se a ser utilizada com cenários em que o motor de sincronização e o SQL se encontram no mesmo servidor. Se utilizar o SQL remoto, recomendamos a utilização de uma conta de serviço gerida por grupo. 

A Conta de Serviço Virtual não pode ser utilizada num Controlador de Domínio devido a problemas de [API (DPAPI) de proteção de dados do Windows.](https://msdn.microsoft.com/library/ms995355.aspx) 

## <a name="managed-service-account"></a>Conta de Serviço Gerida 

Se utilizar um Servidor SQL remoto, recomendamos a utilização de uma conta de serviço gerida pelo grupo. Para obter mais informações sobre como preparar o seu Diretório Ativo para a conta de Serviço Gerido do grupo, consulte a Visão Geral das [Contas de Serviço Geridas pelo Grupo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Para utilizar esta opção, na página [de componentes necessários,](how-to-connect-install-custom.md#install-required-components) selecione **Utilize uma conta de serviço existente** e selecione Conta de Serviço **Gerido**. 

 ![conta de serviço gerido](media/concept-adsync-service-account/account-2.png)

Também é suportado para usar uma conta de serviço gerida autónoma. No entanto, estes apenas podem ser utilizados na máquina local e não há qualquer benefício em usá-los sobre a Conta de Serviço Virtual padrão. 

### <a name="auto-generated-standalone-managed-service-account"></a>Conta de Serviço Gerido Independente Gerada automaticamente 

Se instalar o Azure AD Connect num Controlador de Domínio, uma conta de serviço gerida autónoma é criada pelo assistente de instalação (a menos que especifique a conta a utilizar em definições personalizadas). A conta é pré-fixa **ADSyncMSA_** e usada para o serviço de sincronização real funcionar como. 

Esta conta é uma conta de domínio gerida que não tem uma senha e é gerida automaticamente pelo Windows. 

Esta conta destina-se a ser utilizada com cenários em que o motor de sincronização e o SQL se encontram no Controlador de Domínio. 

## <a name="user-account"></a>Conta de Utilizador 

Uma conta de serviço local é criada pelo assistente de instalação (a menos que especifique a conta a utilizar em configurações personalizadas). A conta é pré-fixa AAD_ e usada para o serviço de sincronização real funcionar como. Se instalar o Azure AD Connect num Controlador de Domínio, a conta é criada no domínio. A conta de serviço AAD_ deve estar localizada no domínio se: 
- você usa um servidor remoto executando SQL Server 
- você usa um proxy que requer autenticação 

 ![conta de utilizador](media/concept-adsync-service-account/account-3.png)

A conta é criada com uma senha complexa e longa que não expira. 

Esta conta é utilizada para armazenar senhas para as outras contas de forma segura. Estas outras palavras-passe de contas são armazenadas encriptadas na base de dados. As chaves privadas das chaves de encriptação estão protegidas com a encriptação de chaves secretas dos serviços criptográficos utilizando a API de Proteção de Dados do Windows (DPAPI). 

Se utilizar um SqL Server completo, então a conta de serviço é o DBO da base de dados criada para o motor de sincronização. O serviço não funcionará como pretendido com qualquer outra permissão. Também é criado um login SQL. 

A conta também é autorizada a ficheiros, chaves de registo e outros objetos relacionados com o Motor de Sincronização. 


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
