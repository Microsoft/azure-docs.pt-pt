---
title: Nova autenticação para as matrizes virtuais do StorSimple | Documentos da Microsoft
description: Explica como utilizar a autenticação do AAD com base para o seu serviço, gerar a nova chave de registo e efetuar o registo manual dos dispositivos.
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61387655"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Utilize a nova autenticação para o StorSimple

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a várias matrizes virtuais do StorSimple. Até o momento, o serviço StorSimple Device Manager usou um serviço de controlo de acesso (ACS) para autenticar o serviço para o seu dispositivo StorSimple. O mecanismo de ACS será preterido em breve e substituído por uma autenticação do Azure Active Directory (AAD).

As informações contidas neste artigo são aplicáveis a ambos os StorSimple 1200 série as matrizes virtuais apenas. Este artigo descreve os detalhes sobre a autenticação do AAD e o associado nova chave de registo de serviço e modificações para as regras de firewall, conforme aplicável para os dispositivos do StorSimple.

A autenticação do AAD ocorre em matrizes virtuais do StorSimple (modelo 1200) que executam a atualização 1 ou posterior.

Devido à introdução da autenticação do AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo do serviço.

Estas alterações são discutidas detalhadamente nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para a autenticação de AAD

Para garantir que o serviço utiliza autenticação baseada no AAD, todos os utilizadores tem de incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar o StorSimple Virtual Array, certifique-se de que o URL seguinte está incluído nas regras da firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos da América |Serviço de autenticação do AAD      |

Para uma lista completa de URL padrões para matrizes virtuais do StorSimple, aceda ao [padrões de URL para regras de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não está incluído nas regras da firewall, além da data de preterição, os utilizadores veem um alerta crítico que o dispositivo StorSimple não foi possível autenticar com o serviço. O serviço não será capaz de comunicar com o dispositivo. Se os utilizadores veem este alerta, terá de incluir o novo URL de autenticação. Para obter mais informações sobre o alerta, aceda a [utilizar alertas para monitorizar o dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Alterações de versão e a autenticação do dispositivo

Se utilizar uma matriz Virtual StorSimple, utilize a tabela seguinte para determinar qual que precisa de tomar a ação com base na versão de software do dispositivo que está a executar.

| Se o dispositivo estiver em execução  | Execute a ação seguinte                                    |
|----------------------------|--------------------------------------------------------------|
| Atualização 1.0 ou posterior e está offline. <br> Verá um alerta que o URL não está na lista de permissões.| 1. Modificar as regras de firewall para incluir o URL de autenticação. Ver [URLs de autenticação](#url-changes-for-aad-authentication). <br> 2. [Obter a chave de registo do AAD do serviço](#aad-based-registration-keys). <br> 3. Execute passos 1 a 5 para [ligar à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Utilize `Invoke-HcsReRegister` cmdlet para registar o dispositivo através do Windows PowerShell. Fornece a chave de que obteve no passo anterior.|
| Atualização 1.0 ou posterior e o dispositivo está online.| Não é necessário realizar qualquer ação.                                       |
| Atualização 0.6 ou anterior e o dispositivo está offline. | 1. [Transferir a atualização 1.0 através do servidor de catálogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Aplicar a atualização 1.0 através da IU da web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Obter a chave de registo do AAD do serviço](#aad-based-registration-keys). <br>4. Execute passos 1 a 5 para [ligar à interface do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Utilize `Invoke-HcsReRegister` cmdlet para registar o dispositivo através do Windows PowerShell. Fornece a chave de que obteve no passo anterior.|
| Atualização 0.6 ou anterior e o dispositivo está online | Modificar as regras de firewall para incluir o URL de autenticação.<br> Instale a atualização 1.0 através do portal do Azure. |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseado no AAD

A iniciar a atualização 1.0 para matrizes virtuais do StorSimple, registro baseado em AAD novas chaves são utilizadas. Utilize as chaves de registo para registar o seu serviço StorSimple Device Manager com o dispositivo.

Se estiver a utilizar um matrizes virtuais do StorSimple que executam a atualização 0.6 ou anterior, é possível utilizar as novas chaves de registo do serviço do AAD. Terá de voltar a gerar a chave de registo do serviço. Depois de regenerar a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A chave antiga já não é válida.

- A nova chave de registo do AAD expira após 3 dias.
- O trabalho de chaves de registo do AAD apenas com virtual de série StorSimple 1200 matrizes em execução de atualização 1 ou posterior. A chave de registo do AAD a partir de um dispositivo da série 8000 do StorSimple não funcionará.
- As chaves de registo do AAD são maiores do que as chaves de registo correspondentes do ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço do AAD

1. Na **StorSimple Device Manager**, aceda à **Management &gt;**  **chaves**.
    
    ![Vá para as chaves](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Clique em **gerar chave**.

    ![Clique em voltar a gerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave antiga já não funciona.

    ![Confirmar voltar a gerar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como implementar [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
