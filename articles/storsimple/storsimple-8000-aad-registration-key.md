---
title: Autenticação AZURE AD para StorSimple 8000 no Gestor de Dispositivos
description: Explica como utilizar a autenticação baseada em AAD para o seu serviço, gerar nova chave de registo e realizar o registo manual dos dispositivos.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 0e071a20051f047efbd040dfc01a30e3c1381367
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631329"
---
# <a name="use-azuer-active-directory-ad-authentication-for-your-storsimple"></a>Utilize a autenticação do Diretório Ativo Azuer (AD) para o seu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager funciona no Microsoft Azure e liga-se a vários dispositivos StorSimple. Até à data, o serviço StorSimple Device Manager utilizou um serviço de Controlo de Acesso (ACS) para autenticar o serviço no seu dispositivo StorSimple. O mecanismo ACS será depreciado em breve e substituído por uma autenticação do Azure Ative Directory (AAD). Para mais informações, aceda aos seguintes anúncios para depreciação acs e utilização da autenticação AAD.

- [O futuro da Azure ACS é o Azure Ative Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Próximas alterações ao Serviço de Controlo de Acesso do Microsoft](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Este artigo descreve os detalhes da autenticação AAD e a nova chave de registo de serviço associada e alterações às regras de firewall aplicáveis aos dispositivos StorSimple. As informações contidas neste artigo aplicam-se apenas aos dispositivos da série StorSimple 8000.

A autenticação AAD ocorre no dispositivo da série StorSimple 8000 em execução do Update 5 ou posterior. Devido à introdução da autenticação AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo de serviço.

Estas alterações são discutidas em pormenor nas seguintes secções.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação AAD

Para garantir que o serviço utiliza a autenticação baseada em AAD, todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar a série StorSimple 8000, certifique-se de que o seguinte URL está incluído nas regras de firewall:

| Padrão url                         | Cloud | Componente/Funcionalidade         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Público |Serviço de autenticação AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos |Serviço de autenticação AAD      |

Para obter uma lista completa de padrões de URL para dispositivos da série StorSimple 8000, vá para [padrões de URL para regras de firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não estiver incluído nas regras de firewall para além da data de depreciação, e o dispositivo estiver a executar a Atualização 5, os utilizadores vêem um alerta de URL. Os utilizadores precisam de incluir o novo URL de autenticação. Se o dispositivo estiver a executar uma versão antes do Update 5, os utilizadores vêem um alerta de batimentocardíaco. Em cada caso, o dispositivo StorSimple não pode autenticar-se com o serviço e o serviço não consegue comunicar com o dispositivo.

## <a name="device-version-and-authentication-changes"></a>Versão do dispositivo e alterações na autenticação

Se utilizar um dispositivo da série StorSimple 8000, utilize a seguinte tabela para determinar que medidas precisa de tomar com base na versão do software do dispositivo que está a executar.

| Se o seu dispositivo estiver em funcionamento| Tome as seguintes medidas                                    |
|--------------------------|------------------------|
| Atualize 5 ou mais tarde e o dispositivo está desligado. <br> Vê um alerta de que a URL não está aprovada.|1. Modifique as regras de firewall para incluir o URL de autenticação. Consulte [URLs de autenticação.](#url-changes-for-aad-authentication)<br>2. [Obtenha a chave de registo AAD do serviço.](#aad-based-registration-keys)<br>3. [Ligue-se à interface Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Utilize `Redo-DeviceRegistration` o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que tem no passo anterior.|
| Atualize 5 ou mais tarde e o dispositivo online.| nenhuma ação necessária.                                       |
| Atualize 4 ou mais cedo e o dispositivo está desligado. |1. Modifique as regras de firewall para incluir o URL de autenticação.<br>2. [Baixar a Atualização 5 através do servidor de catálogos](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Aplicar a atualização 5 através do método hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Obtenha a chave de registo AAD do serviço.](#aad-based-registration-keys)<br>5. [Ligue-se à interface Windows PowerShell do dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Utilize `Redo-DeviceRegistration` o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que tem no passo anterior.|
| Atualização 4 ou mais cedo e o dispositivo está online. |Modifique as regras de firewall para incluir o URL de autenticação.<br> Instale a Atualização 5 através do portal Azure.              |
| A fábrica reiniciou para uma versão antes do Update 5.      |O portal mostra uma chave de registo baseada em AAD enquanto o dispositivo está a executar software mais antigo. Siga os passos no cenário anterior para quando o dispositivo estiver a executar a Atualização 4 ou mais cedo.              |

## <a name="aad-based-registration-keys"></a>Chaves de registo baseadas em AAD

A atualização inicial 5 para dispositivos da série StorSimple 8000, são utilizadas novas chaves de registo baseadas em AAD. Utilize as chaves de registo para registar o seu serviço StorSimple Device Manager com o dispositivo.

Não é possível utilizar as novas chaves de registo de serviço AAD se estiver a utilizar um dispositivo da série StorSimple 8000 em execução do Update 4 ou mais cedo (inclui agora um dispositivo mais antigo).
Neste cenário, é necessário regenerar a chave de registo de serviço. Uma vez regenerada a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A chave velha já não é válida.

- A nova chave de registo AAD expira após 3 dias.
- As teclas de registo AAD funcionam apenas com dispositivos da série StorSimple 8000 em execução da Atualização 5 ou posterior.
- As chaves de registo AAD são mais compridas do que as respetivas chaves de registo ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço AAD

1. No **StorSimple Device Manager,** aceda a **Chaves de Gestão &gt; ** . **Keys** Também pode utilizar a barra de pesquisa para procurar _chaves._
    
2. Clique **na tecla Gerar**.

    ![Clique em regenerar](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais velha não funcionará mais.

    ![Confirmar regenerar](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se estiver a criar um Aparelho StorSimple Cloud no serviço registado no seu dispositivo da série StorSimple 8000, não gere uma chave de registo enquanto a criação estiver em curso. Aguarde que a criação se concretize e, em seguida, gere a chave de registo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implementar [o dispositivo da série StorSimple 8000](storsimple-8000-deployment-walkthrough-u2.md).
