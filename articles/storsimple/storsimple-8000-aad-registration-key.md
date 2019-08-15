---
title: Usar nova autenticação para o serviço Device Manager do StorSimple 8000 no Azure | Microsoft Docs
description: Explica como usar a autenticação baseada no AAD para seu serviço, gerar uma nova chave de registro e executar o registro manual dos dispositivos.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 88badf6de5e7181763dd72c7a51262d16697bffd
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963477"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Usar a nova autenticação para o StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado em Microsoft Azure e se conecta a vários dispositivos StorSimple. Até o momento, o serviço StorSimple Device Manager usou um serviço de controle de acesso (ACS) para autenticar o serviço em seu dispositivo StorSimple. O mecanismo ACS será preterido em breve e substituído por uma autenticação Azure Active Directory (AAD). Para obter mais informações, consulte os comunicados a seguir para a substituição do ACS e o uso da autenticação do AAD.

- [O futuro do Azure ACS é Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Alterações futuras no serviço de controle de acesso da Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Este artigo descreve os detalhes da autenticação do AAD e a nova chave de registro do serviço associada e as modificações nas regras de firewall, conforme aplicável aos dispositivos StorSimple. As informações contidas neste artigo são aplicáveis somente a dispositivos da série StorSimple 8000.

A autenticação do AAD ocorre no dispositivo StorSimple 8000 Series que executa a atualização 5 ou posterior. Devido à introdução da autenticação do AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registro do serviço.

Essas alterações são discutidas em detalhes nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação do AAD

Para garantir que o serviço use a autenticação baseada em AAD, todos os usuários devem incluir as novas URLs de autenticação em suas regras de firewall.

Se estiver usando a série StorSimple 8000, verifique se a seguinte URL está incluída nas regras de firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos da América |Serviço de autenticação do AAD      |

Para obter uma lista completa de padrões de URL para dispositivos da série StorSimple 8000, acesse [padrões de URL para regras de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se a URL de autenticação não estiver incluída nas regras de firewall além da data de reprovação, e o dispositivo estiver executando a atualização 5, os usuários verão um alerta de URL. Os usuários precisam incluir a nova URL de autenticação. Se o dispositivo estiver executando uma versão anterior à atualização 5, os usuários verão um alerta de pulsação. Em cada caso, o dispositivo StorSimple não pode autenticar com o serviço e o serviço não é capaz de se comunicar com o dispositivo.

## <a name="device-version-and-authentication-changes"></a>Alteração de versão do dispositivo e de autenticação

Se estiver usando um dispositivo StorSimple da série 8000, use a tabela a seguir para determinar a ação que você precisa tomar com base na versão do software do dispositivo que você está executando.

| Se o dispositivo estiver em execução| Execute a seguinte ação                                    |
|--------------------------|------------------------|
| Atualização 5 ou posterior e o dispositivo offline. <br> Você verá um alerta de que a URL não está na lista de permissões.|1. Modifique as regras de firewall para incluir a URL de autenticação. Consulte [URLs de autenticação](#url-changes-for-aad-authentication).<br>2. [Obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys).<br>3. [Conecte-se à interface do Windows PowerShell do dispositivo StorSimple 8000 Series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Use `Redo-DeviceRegistration` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 5 ou posterior e o dispositivo online.| Não é necessária nenhuma ação.                                       |
| Atualização 4 ou anterior e o dispositivo offline. |1. Modifique as regras de firewall para incluir a URL de autenticação.<br>2. [Baixe a atualização 5 por meio do servidor de catálogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Aplique a atualização 5 por meio do método de hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys).<br>5. [Conecte-se à interface do Windows PowerShell do dispositivo StorSimple 8000 Series](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Use `Redo-DeviceRegistration` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 4 ou anterior e o dispositivo está online. |Modifique as regras de firewall para incluir a URL de autenticação.<br> Instale a atualização 5 por meio do portal do Azure.              |
| Redefinição de fábrica para uma versão anterior à atualização 5.      |O portal mostra uma chave de registro baseada no AAD enquanto o dispositivo está executando um software mais antigo. Siga as etapas no cenário anterior para quando o dispositivo estiver executando a atualização 4 ou anterior.              |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseadas no AAD

A partir da atualização 5 para dispositivos StorSimple da série 8000, são usadas novas chaves de registro baseadas no AAD. Você usa as chaves de registro para registrar o serviço de Device Manager do StorSimple com o dispositivo.

Você não poderá usar as novas chaves de registro do serviço do AAD se estiver usando um dispositivo StorSimple da série 8000 executando a atualização 4 ou anterior (inclui um dispositivo mais antigo que está sendo ativado agora).
Nesse cenário, você precisa regenerar a chave de registro do serviço. Depois de regenerar a chave, a nova chave é usada para registrar todos os dispositivos subsequentes. A chave antiga não é mais válida.

- A nova chave de registro do AAD expira após 3 dias.
- As chaves de registro do AAD funcionam somente com dispositivos StorSimple da série 8000 que executam a atualização 5 ou posterior.
- As chaves de registro do AAD são maiores do que as chaves de registro do ACS correspondentes.

Execute as etapas a seguir para gerar uma chave de registro do serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registro do serviço do AAD

1. No **StorSimple Device Manager**, vá para **chaves**de  **&gt; gerenciamento** . Você também pode usar a barra de pesquisa para procurar _chaves_.
    
2. Clique em **gerar chave**.

    ![Clique em regenerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais antiga não funcionará mais.

    ![Confirmar regeneração](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se você estiver criando um dispositivo de nuvem StorSimple no serviço registrado em seu dispositivo da série StorSimple 8000, não gere uma chave de registro enquanto a criação estiver em andamento. Aguarde a conclusão da criação e, em seguida, gere a chave de registro.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como implantar o [dispositivo StorSimple 8000 Series](storsimple-8000-deployment-walkthrough-u2.md).

