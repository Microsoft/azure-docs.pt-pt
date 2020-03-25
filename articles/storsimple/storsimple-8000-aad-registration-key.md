---
title: Autenticação Azure AD para StorSimple 8000 no Gestor de Dispositivos
description: Explica como utilizar a autenticação baseada em AAD para o seu serviço, gerar nova chave de registo e realizar o registo manual dos dispositivos.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470909"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Utilize a nova autenticação para o seu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a vários dispositivos StorSimple. Até à data, o serviço StorSimple Device Manager utilizou um serviço de Controlo de Acesso (ACS) para autenticar o serviço ao seu dispositivo StorSimple. O mecanismo ACS será depreciado em breve e substituído por uma autenticação de Diretório Ativo Azure (AAD). Para mais informações, aceda aos seguintes anúncios de deprecação e utilização de autenticação AAD.

- [O futuro do Azure ACS é o Azure Ative Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Próximas alterações ao Serviço de Controlo de Acesso da Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Este artigo descreve os detalhes da autenticação AAD e a nova chave de registo de serviço sitia associada e alterações às regras de firewall conforme aplicável aos dispositivos StorSimple. As informações contidas neste artigo aplicam-se apenas aos dispositivos da série StorSimple 8000.

A autenticação AAD ocorre no dispositivo da série StorSimple 8000 que executa o Update 5 ou mais tarde. Devido à introdução da autenticação AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo de serviço.

Estas alterações são discutidas em pormenor nas seguintes secções.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação AAD

Para garantir que o serviço utiliza a autenticação baseada em AAD, todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar a série StorSimple 8000, certifique-se de que o seguinte URL está incluído nas regras de firewall:

| Padrão URL                         | Nuvem | Componente/Funcionalidade         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Serviço de autenticação AAD      |
| `https://login.microsoftonline.us` | Governo dos EUA |Serviço de autenticação AAD      |

Para obter uma lista completa de padrões de URL para dispositivos da série StorSimple 8000, vá aos [padrões de URL para regras de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não estiver incluído nas regras de firewall para além da data de depreciação, e o dispositivo estiver a executar o Update 5, os utilizadores vêem um alerta de URL. Os utilizadores precisam de incluir o novo URL de autenticação. Se o dispositivo estiver a executar uma versão antes do Update 5, os utilizadores vêem um alerta de batimento cardíaco. Em cada caso, o dispositivo StorSimple não pode autenticar com o serviço e o serviço não é capaz de comunicar com o dispositivo.

## <a name="device-version-and-authentication-changes"></a>Versão do dispositivo e alterações de autenticação

Se utilizar um dispositivo da série StorSimple 8000, utilize a tabela seguinte para determinar que medidas precisa de tomar com base na versão do software do dispositivo que está a executar.

| Se o seu dispositivo estiver em execução| Tome as seguintes medidas                                    |
|--------------------------|------------------------|
| Atualização 5 ou mais tarde e o dispositivo está offline. <br> Vê-se um alerta de que a URL não está na lista de permissões.|1. Modificar as regras de firewall para incluir o URL de autenticação. Ver [URLs](#url-changes-for-aad-authentication)de autenticação .<br>2. [Obtenha a chave de registo AAD do serviço](#aad-based-registration-keys).<br>3. [Ligue-se à interface Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Utilize `Redo-DeviceRegistration` cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que obteve no passo anterior.|
| Atualização 5 ou mais tarde e o dispositivo on-line.| nenhuma ação necessária.                                       |
| Atualização 4 ou mais cedo e o dispositivo está offline. |1. Modificar as regras de firewall para incluir o URL de autenticação.<br>2. [Descarregue a Atualização 5 através do servidor de catálogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Aplicar a atualização 5 através do método de fixação](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix)de calor .<br>4. [Obtenha a chave de registo AAD do serviço](#aad-based-registration-keys).<br>5. [Ligue-se à interface Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Utilize `Redo-DeviceRegistration` cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que obteve no passo anterior.|
| Atualização 4 ou mais cedo e o dispositivo está on-line. |Modifique as regras de firewall para incluir o URL de autenticação.<br> Instale a Atualização 5 através do portal Azure.              |
| A fábrica reset para uma versão antes da Atualização 5.      |O portal mostra uma chave de registo baseada em AAD enquanto o dispositivo está a executar software mais antigo. Siga os passos no cenário anterior para quando o dispositivo estiver a executar o Update 4 ou mais cedo.              |

## <a name="aad-based-registration-keys"></a>Chaves de registo baseadas em AAD

Início da Atualização 5 para dispositivos da série StorSimple 8000, são utilizadas novas teclas de registo baseadas em AAD. Utiliza as chaves de registo para registar o serviço StorSimple Device Manager com o dispositivo.

Não é possível utilizar as novas teclas de registo do serviço AAD se estiver a utilizar um dispositivo da série StorSimple 8000 a executar o Update 4 ou mais cedo (inclui um dispositivo mais antigo a ser ativado agora).
Neste cenário, é necessário regenerar a chave de registo de serviço. Uma vez regenerado a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A velha chave já não é válida.

- A nova chave de registo aAD expira após 3 dias.
- As teclas de registo AAD funcionam apenas com dispositivos da série StorSimple 8000 que executam o Update 5 ou mais tarde.
- As chaves de registo AAD são mais compridas do que as respetivas chaves de registo ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço AAD

1. No **StorSimple Device Manager,** vá à **Management &gt;** **Keys**. Também pode utilizar a barra de pesquisa para procurar _Chaves_.
    
2. Clique na **tecla Generate**.

    ![Clique em regenerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais velha não funcionará mais.

    ![Confirmar regeneração](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se estiver a criar um StorSimple Cloud Appliance no serviço registado no seu dispositivo da série StorSimple 8000, não gere uma chave de registo enquanto a criação estiver em curso. Aguarde que a criação termine e, em seguida, gere a chave de registo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implementar o [dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).

