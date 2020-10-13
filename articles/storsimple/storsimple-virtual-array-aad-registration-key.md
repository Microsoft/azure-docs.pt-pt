---
title: Nova autenticação para Arrays Virtuais StorSimple
description: Saiba mais sobre a autenticação AAD, a nova chave de registo de serviços associada e alterações às regras de firewall aplicáveis aos dispositivos StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: d750b3a85741bb4761b640848f01e5917760b5aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261095"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Utilize a nova autenticação para o seu StorSimple

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O serviço StorSimple Device Manager funciona no Microsoft Azure e liga-se a várias Matrizes Virtuais StorSimple. Até à data, o serviço StorSimple Device Manager utilizou um serviço de Controlo de Acesso (ACS) para autenticar o serviço no seu dispositivo StorSimple. O mecanismo ACS será depreciado em breve e substituído por uma autenticação do Azure Ative Directory (AAD).

As informações contidas neste artigo aplicam-se apenas a Matrizes Virtuais da Série StorSimple 1200. Este artigo descreve os detalhes da autenticação AAD e a nova chave de registo de serviço associada e alterações às regras de firewall aplicáveis aos dispositivos StorSimple.

A autenticação AAD ocorre em StorSimple Virtual Arrays (modelo 1200) executando a Atualização 1 ou posterior.

Devido à introdução da autenticação AAD, as alterações ocorrem em:

- Padrões de URL para regras de firewall.
- Chave de registo de serviço.

Estas alterações são discutidas em pormenor nas seguintes secções.

## <a name="url-changes-for-aad-authentication"></a>Alterações de URL para autenticação AAD

Para garantir que o serviço utiliza a autenticação baseada em AAD, todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall.

Se utilizar o StorSimple Virtual Array, certifique-se de que o seguinte URL está incluído nas regras de firewall:

| Padrão url                         | Cloud | Componente/Funcionalidade         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Público |Serviço de autenticação AAD      |
| `https://login.microsoftonline.us` | Governo dos Estados Unidos |Serviço de autenticação AAD      |

Para obter uma lista completa de padrões de URL para StorSimple Virtual Arrays, vá a [padrões de URL para regras de firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se o URL de autenticação não estiver incluído nas regras de firewall para além da data de depreciação, os utilizadores vêem um alerta crítico de que o seu dispositivo StorSimple não poderia autenticar com o serviço. O serviço não será capaz de comunicar com o dispositivo. Se os utilizadores virem este alerta, têm de incluir o novo URL de autenticação. Para obter mais informações sobre o alerta, aceda a [alertas de utilização para monitorizar o seu dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Versão do dispositivo e alterações na autenticação

Se utilizar um Conjunto Virtual StorSimple, utilize a seguinte tabela para determinar que medidas precisa de tomar com base na versão do software do dispositivo que está a executar.

| Se o seu dispositivo estiver em funcionamento  | Tome as seguintes medidas                                    |
|----------------------------|--------------------------------------------------------------|
| Atualize 1.0 ou mais tarde e está offline. <br> Vê um alerta de que a URL não está na lista branca.| 1. Modifique as regras de firewall para incluir o URL de autenticação. Consulte [URLs de autenticação.](#url-changes-for-aad-authentication) <br> 2. [Obtenha a chave de registo AAD do serviço.](#aad-based-registration-keys) <br> 3. Executar os passos 1-5 para [ligar à interface Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Utilize `Invoke-HcsReRegister` o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que tem no passo anterior.|
| Atualize 1.0 ou mais tarde e o dispositivo está online.| nenhuma ação necessária.                                       |
| Atualize 0.6 ou mais cedo e o dispositivo está desligado. | 1. [Baixar a atualização 1.0 através do servidor de catálogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Aplicar atualização 1.0 através da UI web local](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Obtenha a chave de registo AAD do serviço](#aad-based-registration-keys). <br>4. Executar os passos 1-5 para [ligar à interface Windows PowerShell da matriz virtual](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Utilize `Invoke-HcsReRegister` o cmdlet para registar o dispositivo através do Windows PowerShell. Forneça a chave que tem no passo anterior.|
| Atualização 0.6 ou mais cedo e o dispositivo está online | Modifique as regras de firewall para incluir o URL de autenticação.<br> Instale a Atualização 1.0 através do portal Azure. |

## <a name="aad-based-registration-keys"></a>Chaves de registo baseadas em AAD

Atualização inicial 1.0 para Arrays Virtuais StorSimple, são utilizadas novas chaves de registo baseadas em AAD. Utilize as chaves de registo para registar o seu serviço StorSimple Device Manager com o dispositivo.

Não é possível utilizar as novas chaves de registo de serviço AAD se estiver a utilizar um StorSimple Virtual Arrays em execução de Update 0.6 ou mais cedo. Precisa regenerar a chave de registo de serviço. Uma vez regenerada a chave, a nova chave é utilizada para registar todos os dispositivos subsequentes. A chave velha já não é válida.

- A nova chave de registo AAD expira após 3 dias.
- As teclas de registo AAD funcionam apenas com matrizes virtuais da série StorSimple 1200 que executam a Atualização 1 ou posterior. A chave de registo AAD de um dispositivo da série StorSimple 8000 não funcionará.
- As chaves de registo AAD são mais compridas do que as respetivas chaves de registo ACS.

Execute os seguintes passos para gerar uma chave de registo de serviço AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Para gerar a chave de registo de serviço AAD

1. No **StorSimple Device Manager,** aceda a **Chaves de Gestão &gt; ** . **Keys**
    
    ![Ir para Chaves](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Clique **na tecla Gerar**.

    ![Clique em regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copie a nova chave. A chave mais velha já não funciona.

    ![Confirmar regenerar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como implementar [o StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
