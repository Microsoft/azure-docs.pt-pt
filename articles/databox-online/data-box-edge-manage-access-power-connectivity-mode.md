---
title: Acesso de dispositivo do Edge de caixa de dados do Microsoft Azure, power e modo de conectividade | Documentos da Microsoft
description: Descreve como gerir o acesso, energia e modo de conectividade para o dispositivo de limite de caixa de dados do Azure que ajuda a transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e0aa3468bda9f904d62e9e20545ac5f990cef521
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57902147"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>Gerir o acesso, energia e modo de conectividade para o Edge de caixa de dados do Azure (pré-visualização)

Este artigo descreve como gerir o modo de acesso, energia e conectividade para sua caixa de dados do Azure Edge. Estas operações são efetuadas através da IU da web local ou o portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Gerir o acesso do dispositivo
> * Gerenciar o modo de conectividade
> * Gerenciar a energia

> [!IMPORTANT]
> O Data Box Edge está em pré-visualização. Reveja os [Termos de serviço do Azure para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de encomendar e implementar esta solução.

## <a name="manage-device-access"></a>Gerir o acesso do dispositivo

O acesso ao seu dispositivo Edge de caixa de dados é controlado pelo uso de uma palavra-passe de administrador do dispositivo. Pode alterar a palavra-passe de administrador através da IU da web local. Também pode repor a palavra-passe de administrador do dispositivo no portal do Azure.

### <a name="change-device-administrator-password"></a>Alterar senha de administrador do dispositivo

Siga estes passos na IU do local para alterar a palavra-passe de administrador do dispositivo.

1. Na IU da web local, aceda a **manutenção > alteração de palavra-passe**.
2. Introduza a palavra-passe atual e, em seguida, a nova palavra-passe. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Certifique-se a nova palavra-passe.

    ![Alterar palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selecione **alterar palavra-passe**.
 
### <a name="reset-device-administrator-password"></a>Repor palavra-passe de administrador do dispositivo

O fluxo de trabalho de reposição não exige que o usuário de recuperar a palavra-passe antiga e é útil quando a palavra-passe é perdida. Este fluxo de trabalho é executado no portal do Azure.

1. No portal do Azure, aceda a **descrição geral > Repor a palavra-passe do administrador**.

    ![Repor palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Introduza a palavra-passe nova e, em seguida, confirme-lo. A palavra-passe fornecida tem de ter entre 8 e 16 carateres. A palavra-passe tem de ter 3 dos seguintes carateres: carateres em maiúsculas, minúsculas, numéricos e especiais. Selecione **repor**.

    ![Repor palavra-passe](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gerenciar o modo de conectividade

Para além do modo predefinido de totalmente conectada, o dispositivo também pode executar no modo parcialmente conectado, ou completamente desligado. Cada um desses modos é descrita abaixo:

- **Totalmente conectada** -este é o modo padrão normal em que o dispositivo funciona. O carregamento de cloud e a transferência de dados está ativado neste modo. Pode utilizar o portal do Azure ou a IU da web local para gerir o dispositivo.

- **Parcialmente desligado** – neste modo, o dispositivo não é possível carregar quaisquer dados para as partilhas no entanto podem ser gerenciados através do portal do Azure.

    Este modo é normalmente utilizado quando numa rede com tráfego limitado da satélite e o objetivo é minimizar o consumo de largura de banda de rede. Consumo de rede mínima ainda pode ocorrer para operações de monitorização de dispositivos.

- **Desligado** – neste modo, o dispositivo estiver totalmente desligado da cloud e ambos os cloud carregamentos e transferências estão desativadas. O dispositivo só pode ser gerido através da IU da web local.

    Este modo é normalmente utilizado quando deseja colocar offline o seu dispositivo.

Para alterar o modo do dispositivo, siga estes passos:

1. Na IU da web local do seu dispositivo, aceda a **configuração > definições da Cloud**.
2. Na lista pendente, selecione o modo que deseja operar do dispositivo. Pode selecionar a partir **totalmente conectada**, **parcialmente ligado**, e **totalmente desligado**. Para executar o dispositivo no modo desligado parcialmente, ative **do Azure management portal**.

    ![Modo de conetividade](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gerenciar a energia

Pode encerrar ou reiniciar o dispositivo físico com a IU web local. Nós recomendamos que antes de reiniciar, dar as partilhas offline no servidor de dados e, em seguida, o dispositivo. Esta ação minimiza qualquer possibilidade de Corrupção de dados.

1. Na IU da web local, aceda a **manutenção > definições de energia**.
2. Selecione **encerramento** ou **reiniciar** consoante o que pretende fazer.

    ![Definições de energia](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando lhe for pedida confirmação, selecione **Sim** para continuar.

> [!NOTE]
> Se encerrar o dispositivo físico, terá de enviar o botão de energia no dispositivo para ativá-la.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [gerir partilhas](data-box-edge-manage-shares.md).