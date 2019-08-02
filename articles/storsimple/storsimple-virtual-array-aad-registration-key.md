---
title: Nova autenticação para matrizes virtuais StorSimple | Microsoft Docs
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 723d5e969ba2f635724ffa50d562a7abaf936dcf
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517128"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Usar a nova autenticação para o StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O serviço StorSimple Device Manager é executado em Microsoft Azure e se conecta a várias matrizes virtuais StorSimple. Até o momento, o serviço StorSimple Device Manager usou um serviço de controle de acesso (ACS) para autenticar o serviço em seu dispositivo StorSimple. O mecanismo ACS será preterido em breve e substituído por uma autenticação Azure Active Directory (AAD).

As informações contidas neste artigo são aplicáveis somente às matrizes virtuais do StorSimple 1200 Series. Este artigo descreve os detalhes da autenticação do AAD e a nova chave de registro do serviço associada e as modificações nas regras de firewall, conforme aplicável aos dispositivos StorSimple.

A autenticação do AAD ocorre em matrizes virtuais StorSimple (modelo 1200) executando a atualização 1 ou posterior.

Devido à introdução da autenticação do AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registro do serviço.

Essas alterações são discutidas em detalhes nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação do AAD

Para garantir que o serviço use a autenticação baseada em AAD, todos os usuários devem incluir as novas URLs de autenticação em suas regras de firewall.

Se estiver usando o StorSimple virtual array, verifique se a seguinte URL está incluída nas regras de firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos da América |Serviço de autenticação do AAD      |

Para obter uma lista completa de padrões de URL para matrizes virtuais StorSimple, acesse [padrões de URL para regras de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se a URL de autenticação não estiver incluída nas regras de firewall além da data de reprovação, os usuários verão um alerta crítico de que seu dispositivo StorSimple não pôde autenticar com o serviço. O serviço não poderá se comunicar com o dispositivo. Se os usuários veem esse alerta, eles precisam incluir a nova URL de autenticação. Para obter mais informações sobre o alerta, acesse [usar alertas para monitorar seu dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Alteração de versão do dispositivo e de autenticação

Se estiver usando uma matriz virtual StorSimple, use a tabela a seguir para determinar a ação que você precisa tomar com base na versão do software do dispositivo que você está executando.

| Se o dispositivo estiver em execução  | Execute a seguinte ação                                    |
|----------------------------|--------------------------------------------------------------|
| Atualize o 1,0 ou posterior e esteja offline. <br> Você verá um alerta de que a URL não está na lista de permissões.| 1. Modifique as regras de firewall para incluir a URL de autenticação. Consulte [URLs de autenticação](#url-changes-for-aad-authentication). <br> 2. [Obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys). <br> 3. Execute as etapas 1-5 para [se conectar à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Use `Invoke-HcsReRegister` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualize o 1,0 ou posterior e o dispositivo está online.| Não é necessária nenhuma ação.                                       |
| Atualize o 0,6 ou anterior e o dispositivo está offline. | 1. [Baixe a atualização 1,0 por meio do servidor de catálogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Aplique a atualização 1,0 por meio da interface do usuário da Web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Obtenha a chave de registro do AAD do serviço](#aad-based-registration-keys). <br>4. Execute as etapas 1-5 para [se conectar à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Use `Invoke-HcsReRegister` o cmdlet para registrar o dispositivo por meio do Windows PowerShell. Forneça a chave que você obteve na etapa anterior.|
| Atualização 0,6 ou anterior e o dispositivo está online | Modifique as regras de firewall para incluir a URL de autenticação.<br> Instale a atualização 1,0 por meio do portal do Azure. |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseadas no AAD

A partir da atualização 1,0 para matrizes virtuais do StorSimple, são usadas novas chaves de registro baseadas no AAD. Você usa as chaves de registro para registrar o serviço de Device Manager do StorSimple com o dispositivo.

Você não poderá usar as novas chaves de registro do serviço do AAD se estiver usando uma matriz virtual do StorSimple executando a atualização 0,6 ou anterior. Você precisa regenerar a chave de registro do serviço. Depois de regenerar a chave, a nova chave é usada para registrar todos os dispositivos subsequentes. A chave antiga não é mais válida.

- A nova chave de registro do AAD expira após 3 dias.
- As chaves de registro do AAD funcionam somente com matrizes virtuais do StorSimple série 1200 executando a atualização 1 ou posterior. A chave de registro do AAD de um dispositivo StorSimple da série 8000 não funcionará.
- As chaves de registro do AAD são maiores do que as chaves de registro do ACS correspondentes.

Execute as etapas a seguir para gerar uma chave de registro do serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registro do serviço do AAD

1. No **StorSimple Device Manager**, vá para **chaves**de  **&gt; gerenciamento** .
    
    ![Ir para chaves](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Clique em **gerar chave**.

    ![Clique em regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais antiga não funciona mais.

    ![Confirmar regeneração](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como implantar o [StorSimple virtual array](storsimple-virtual-array-deploy1-portal-prep.md)
