---
title: Acesso de dispositivo de Gateway de caixa de dados do Microsoft Azure, power e modo de conectividade | Documentos da Microsoft
description: Descreve como gerir o acesso, energia e modo de conectividade para o dispositivo de Gateway de caixa de dados do Azure que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d46d74544181a6b9cbfd049b2f5461b20b928483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401436"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gerir o acesso, energia e modo de conectividade para o Gateway de caixa de dados do Azure

Este artigo descreve como gerir o modo de conectividade, power e acesso para o Gateway de caixa de dados do Azure. Estas operações são efetuadas através da IU da web local ou o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir o acesso do dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar a energia

## <a name="manage-device-access"></a>Gerir o acesso do dispositivo

O acesso ao seu dispositivo de Gateway de caixa de dados é controlado pelo uso de uma palavra-passe do dispositivo. Pode alterar a palavra-passe através da IU da web local. Também pode repor a palavra-passe do dispositivo no portal do Azure.

### <a name="change-device-password"></a>Alterar a palavra-passe do dispositivo

Siga estes passos na IU do local para alterar a palavra-passe do dispositivo.

1. Na IU da web local, aceda a **manutenção > alteração de palavra-passe**.
2. Introduza a palavra-passe atual e, em seguida, a nova palavra-passe. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Certifique-se a nova palavra-passe.

    ![Alterar palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Clique em **alterar palavra-passe**.
 
### <a name="reset-device-password"></a>Repor palavra-passe do dispositivo

O fluxo de trabalho de reposição não exige que o usuário de recuperar a palavra-passe antiga e é útil quando a palavra-passe é perdida. Este fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, aceda a **descrição geral > Repor a palavra-passe do administrador**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Introduza a palavra-passe nova e, em seguida, confirme-lo. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Clique em **repor**.

    ![Repor palavra-passe](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Além do modo normal padrão, o seu dispositivo também pode executar no modo desligado parcialmente ou desligado. Cada um desses modos é descrita abaixo:

- **Parcialmente desligado** – neste modo, o dispositivo não é possível carregar quaisquer dados para as partilhas no entanto podem ser gerenciados através do portal do Azure.

    Este modo é normalmente utilizado quando numa rede com tráfego limitado da satélite e o objetivo é minimizar o consumo de largura de banda de rede. Consumo de rede mínima ainda pode ocorrer para operações de monitorização de dispositivos.

- **Desligado** – neste modo, o dispositivo estiver totalmente desligado da cloud e ambos os cloud carregamentos e transferências estão desativadas. O dispositivo só pode ser gerido através da IU da web local.

    Este modo é normalmente utilizado quando deseja colocar offline o seu dispositivo.

Para alterar o modo do dispositivo, siga estes passos:

1. Na IU da web local do seu dispositivo, aceda a **configuração > definições da Cloud**.
2. Desativar a **Cloud carregamento e transferência**.
3. Para executar o dispositivo no modo desligado parcialmente, ative **do Azure management portal**.

    ![Modo de conetividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Para executar o dispositivo no modo desligado, desative **do Azure management portal**. Agora, o dispositivo só pode ser gerido através da IU da web local.

    ![Modo de conetividade](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gerenciar a energia

Pode encerrar ou reiniciar o seu dispositivo, físico e virtual, com a IU web local. Recomendamos que antes de reiniciar, coloque as partilhas offline no anfitrião e, em seguida, no dispositivo. Esta ação minimiza qualquer possibilidade de Corrupção de dados.

1. Na IU da web local, aceda a **manutenção > definições de energia**.
2. Clique em **encerramento** ou **reiniciar** consoante o que pretende fazer.

    ![Definições de energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando lhe for pedida confirmação, clique em **Sim** para continuar.

> [!NOTE]
> Se encerrar o dispositivo virtual, terá de iniciar o dispositivo através do gerenciamento de hipervisor.
