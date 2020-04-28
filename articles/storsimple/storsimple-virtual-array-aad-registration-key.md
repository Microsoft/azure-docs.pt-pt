---
title: Nova autenticação para Matrizes Virtuais StorSimple
description: Explica como utilizar a autenticação baseada em AAD para o seu serviço, gerar nova chave de registo e realizar o registo manual dos dispositivos.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76273812"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Utilize a nova autenticação para o seu StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a várias Matrizes Virtuais StorSimple. Até à data, o serviço StorSimple Device Manager utilizou um serviço de Controlo de Acesso (ACS) para autenticar o serviço ao seu dispositivo StorSimple. O mecanismo ACS será depreciado em breve e substituído por uma autenticação de Diretório Ativo Azure (AAD).

As informações contidas neste artigo aplicam-se apenas a ambos os Arrays Virtuais da Série StorSimple 1200. Este artigo descreve os detalhes da autenticação AAD e a nova chave de registo de serviço sitia associada e alterações às regras de firewall conforme aplicável aos dispositivos StorSimple.

A autenticação AAD ocorre em StorSimple Virtual Arrays (modelo 1200) executando Update 1 ou posterior.

Devido à introdução da autenticação AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo de serviço.

Estas alterações são discutidas em pormenor nas seguintes secções.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação AAD

Para garantir que o serviço utiliza a autenticação baseada em AAD, todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar o StorSimple Virtual Array, certifique-se de que o seguinte URL está incluído nas regras de firewall:

| Padrão URL                         | Nuvem | Componente/Funcionalidade         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Público |Serviço de autenticação AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos |Serviço de autenticação AAD      |

Para obter uma lista completa de padrões de URL para StorSimple Virtual Arrays, vá aos [padrões de URL para regras de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não estiver incluído nas regras de firewall para além da data de depreciação, os utilizadores vêem um alerta crítico de que o seu dispositivo StorSimple não poderia autenticar com o serviço. O serviço não poderá comunicar com o dispositivo. Se os utilizadores virem este alerta, têm de incluir o novo URL de autenticação. Para mais informações sobre o alerta, vá a [Utilizar alertas para monitorizar o seu dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Versão do dispositivo e alterações de autenticação

Se utilizar um StorSimple Virtual Array, utilize a tabela seguinte para determinar que medidas precisa de tomar com base na versão do software do dispositivo que está a executar.

| Se o seu dispositivo estiver em execução  | Tome as seguintes medidas                                    |
|----------------------------|--------------------------------------------------------------|
| Atualização 1.0 ou mais tarde e está offline. <br> Vê-se um alerta de que a URL não está na lista branca.| 1. Modificar as regras de firewall para incluir o URL de autenticação. Ver [URLs](#url-changes-for-aad-authentication)de autenticação . <br> 2. [Obtenha a chave de registo AAD do serviço](#aad-based-registration-keys). <br> 3. Execute os passos 1-5 para [ligar à interface Do Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. `Invoke-HcsReRegister` Utilize o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que obteve no passo anterior.|
| Atualização 1.0 ou mais tarde e o dispositivo está online.| nenhuma ação necessária.                                       |
| Atualização 0.6 ou mais cedo e o dispositivo está offline. | 1. [Descarregue a Atualização 1.0 através do servidor](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)de catálogo .<br>2. [Aplicar a atualização 1.0 através da UI web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Obtenha a chave de registo AAD do serviço](#aad-based-registration-keys). <br>4. Execute os passos 1-5 para [ligar à interface Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. `Invoke-HcsReRegister` Utilize o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que obteve no passo anterior.|
| Atualização 0.6 ou mais cedo e o dispositivo está online | Modifique as regras de firewall para incluir o URL de autenticação.<br> Instale a Atualização 1.0 através do portal Azure. |

## <a name="aad-based-registration-keys"></a>Chaves de registo baseadas em AAD

Atualização inicial 1.0 para Matrizes Virtuais StorSimple, são utilizadas novas teclas de registo baseadas em AAD. Utiliza as chaves de registo para registar o serviço StorSimple Device Manager com o dispositivo.

Não é possível utilizar as novas teclas de registo do serviço AAD se estiver a utilizar um StorSimple Virtual Arrays a executar o Update 0.6 ou mais cedo. Precisa regenerar a chave de registo de serviço. Uma vez regenerado a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A velha chave já não é válida.

- A nova chave de registo aAD expira após 3 dias.
- As teclas de registo AAD funcionam apenas com matrizes virtuais da série StorSimple 1200 a executar o Update 1 ou mais tarde. A chave de registo AAD de um dispositivo da série StorSimple 8000 não funcionará.
- As chaves de registo AAD são mais compridas do que as respetivas chaves de registo ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço AAD

1. No **StorSimple Device Manager,** vá a **Chaves**de **Gestão. &gt; **
    
    ![Ir a Keys](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Clique na **tecla Generate**.

    ![Clique em regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais velha já não funciona.

    ![Confirmar regeneração](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implementar [o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
