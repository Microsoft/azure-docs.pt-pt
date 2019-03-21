---
title: Utilizar a autenticação de novo para o serviço de Gestor de dispositivos do StorSimple 8000 no Azure | Documentos da Microsoft
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 01d36188c1684eae8303cb20ba0fd0c708ff91ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079918"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Utilize a nova autenticação para o StorSimple

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a diversos dispositivos StorSimple. Até o momento, o serviço StorSimple Device Manager usou um serviço de controlo de acesso (ACS) para autenticar o serviço para o seu dispositivo StorSimple. O mecanismo de ACS será preterido em breve e substituído por uma autenticação do Azure Active Directory (AAD). Para obter mais informações, vá para os seguintes anúncios de depreciação de ACS e a utilização de autenticação do AAD.

- [O futuro do Azure ACS é o Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Alterações futuras ao serviço de controlo de acesso da Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Este artigo descreve os detalhes sobre a autenticação do AAD e o associado nova chave de registo de serviço e modificações para as regras de firewall, conforme aplicável para os dispositivos do StorSimple. As informações contidas neste artigo são aplicáveis apenas a dispositivos série StorSimple 8000.

Ocorre a autenticação do AAD no dispositivo da série StorSimple 8000 que executam a atualização 5 ou posterior. Devido à introdução da autenticação do AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo do serviço.

Estas alterações são discutidas detalhadamente nas seções a seguir.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para a autenticação de AAD

Para garantir que o serviço utiliza autenticação baseada no AAD, todos os utilizadores tem de incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar a série StorSimple 8000, certifique-se de que o URL seguinte está incluído nas regras da firewall:

| Padrão de URL                         | Nuvem | Componente/funcionalidade         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Público do Azure |Serviço de autenticação do AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos da América |Serviço de autenticação do AAD      |

Para uma lista completa de URL padrões para dispositivos da série StorSimple 8000, aceda ao [padrões de URL para regras de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não está incluído nas regras da firewall, além da data de preterição e o dispositivo está a executar a atualização 5, os utilizadores veem um alerta de URL. Os utilizadores tem de incluir o novo URL de autenticação. Se o dispositivo está a executar uma versão anterior à atualização 5, os utilizadores veem um alerta de heartbeat. Em cada caso, o dispositivo StorSimple não é possível autenticar com o serviço e o serviço não é capaz de comunicar com o dispositivo.

## <a name="device-version-and-authentication-changes"></a>Alterações de versão e a autenticação do dispositivo

Se utilizar um dispositivo da série StorSimple 8000, utilize a tabela seguinte para determinar qual que precisa de tomar a ação com base na versão de software do dispositivo que está a executar.

| Se o dispositivo estiver em execução| Execute a ação seguinte                                    |
|--------------------------|------------------------|
| Atualização 5 ou posterior e o dispositivo está offline. <br> Verá um alerta que o URL não está na lista de permissões.|1. Modificar as regras de firewall para incluir o URL de autenticação. Ver [URLs de autenticação](#url-changes-for-aad-authentication).<br>2. [Obter a chave de registo do AAD do serviço](#aad-based-registration-keys).<br>3. [Ligar à interface do Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Utilize `Redo-DeviceRegistration` cmdlet para registar o dispositivo através do Windows PowerShell. Fornece a chave de que obteve no passo anterior.|
| Atualização 5 ou posterior e o dispositivo online.| Não é necessário realizar qualquer ação.                                       |
| Atualização 4 ou anterior e o dispositivo está offline. |1. Modificar as regras de firewall para incluir o URL de autenticação.<br>2. [Transferir a atualização 5 por meio do servidor de catálogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Aplicar a atualização 5 o método de correção](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Obter a chave de registo do AAD do serviço](#aad-based-registration-keys).<br>5. [Ligar à interface do Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Utilize `Redo-DeviceRegistration` cmdlet para registar o dispositivo através do Windows PowerShell. Fornece a chave de que obteve no passo anterior.|
| Atualização 4 ou anterior e o dispositivo está online. |Modificar as regras de firewall para incluir o URL de autenticação.<br> Instale atualização 5 através do portal do Azure.              |
| Reposição de fábrica para uma versão antes da atualização 5.      |O portal mostra uma chave de registo com base do AAD, enquanto o dispositivo está a executar o software mais antiga. Siga os passos no cenário anterior para quando o dispositivo é executar a atualização 4 ou anterior.              |

## <a name="aad-based-registration-keys"></a>Chaves de registro baseado no AAD

A partir do Update 5 para dispositivos da série StorSimple 8000, registro baseado em AAD novas chaves são utilizadas. Utilize as chaves de registo para registar o seu serviço StorSimple Device Manager com o dispositivo.

Não é possível utilizar as novas chaves de registo do serviço do AAD, se estiver a utilizar um dispositivo da série StorSimple 8000 que executam a atualização 4 ou anterior (inclui um dispositivo mais antigo a ser ativado agora).
Neste cenário, terá de voltar a gerar a chave de registo do serviço. Depois de regenerar a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A chave antiga já não é válida.

- A nova chave de registo do AAD expira após 3 dias.
- As chaves de registo AAD, funcionam somente com dispositivos da série StorSimple 8000 com Update 5 ou posteriores.
- As chaves de registo do AAD são maiores do que as chaves de registo correspondentes do ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço do AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço do AAD

1. Na **StorSimple Device Manager**, aceda à **Management &gt;**  **chaves**. Também pode utilizar a barra de pesquisa para procurar _chaves_.
    
2. Clique em **gerar chave**.

    ![Clique em voltar a gerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave antiga já não irá funcionar.

    ![Confirmar voltar a gerar](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se estiver a criar uma StorSimple Cloud Appliance no serviço registado no seu dispositivo da série StorSimple 8000, não gere uma chave de registo enquanto a criação está em curso. Aguarde até a criação seja concluída e, em seguida, gerar a chave de registo.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como implementar [dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

