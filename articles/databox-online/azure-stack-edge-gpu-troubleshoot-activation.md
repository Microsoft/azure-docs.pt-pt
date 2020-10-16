---
title: Utilize o portal Azure para resolver problemas de ativação relacionados com o Azure Stack Edge Pro com GPU Microsoft Docs
description: Descreve como resolver problemas com a ativação gpu do Azure Stack Edge Pro e questões relacionadas com o cofre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: alkohli
ms.openlocfilehash: 33254c170c309626ecfa9099bc4d86578148f4c1
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941388"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Problemas de ativação de resolução de problemas no seu dispositivo GPU Azure Stack Edge Pro 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como resolver problemas de ativação no seu dispositivo GPU Azure Stack Edge Pro. 


## <a name="activation-errors"></a>Erros de ativação

O quadro seguinte resume os erros relacionados com a ativação do dispositivo e a resolução recomendada correspondente.

| Mensagem de erro| Resolução recomendada |
|------------------------------------------------------|--------------------------------------|
| Se o Cofre da Chave Azure utilizado para ativação for eliminado antes de o dispositivo ser ativado com a chave de ativação, então receberá este erro. <br> ![Erro do cofre da chave 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Se o cofre da chave tiver sido apagado, pode recuperar o cofre se o cofre estiver em duração de proteção de purga. Siga os passos em [Recuperar um cofre chave.](/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault) <br>Se a duração da proteção da purga tiver decorrido, o cofre da chave não pode ser recuperado. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| Se o Cofre da Chave Azure for eliminado após a ativação do dispositivo, e tentar efetuar qualquer operação que envolva encriptação, por exemplo: **Adicionar Utilizador**, **Adicionar Share**, **Configurar Compute**, então recebe este erro. <br> ![Erro do cofre da chave 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Se o cofre da chave tiver sido apagado, pode recuperar o cofre se o cofre estiver em duração de proteção de purga. Siga os passos em Recuperar um cofre chave. <br>Se a duração da proteção da purga tiver decorrido, o cofre da chave não pode ser recuperado. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| Se a Chave de Integridade do Canal no Cofre da Chave Azure for eliminada e você, tente efetuar quaisquer operações que envolvam encriptação, por exemplo: **Adicionar Utilizador**, **Adicionar Share**, **Configurar Compute** - então receberá este erro. <br> ![Erro do cofre da chave 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Se a chave de integridade do canal no cofre da chave for eliminada, mas ainda estiver dentro da duração da purga, siga os passos na [remoção da chave do cofre do undo-key](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Se a duração da proteção da purga tiver decorrido, e se tiver a chave apoiada, pode restaurar a partir da cópia de segurança que não conseguirá recuperar a chave. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| Se a geração da chave de ativação falhar devido a qualquer erro, receberá este erro. Estão presentes mais detalhes na notificação. <br> ![Erro do cofre da chave 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Espere alguns minutos e re-teste da operação. Se o problema persistir, contacte o Microsoft Support. |
| Se o utilizador tiver permissões apenas de leitura, então o utilizador não está autorizado a gerar uma chave de ativação e este erro é apresentado. <br> ![Erro do cofre da chave 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Isto pode ser porque não tem o acesso certo ou o  *Microsoft.KeyVault* não está registado.<li>Certifique-se de que tem acesso ao proprietário ou ao contribuinte ao nível do grupo de recursos utilizado para o seu recurso Azure Stack Edge.</li><li>Certifique-se de que o fornecedor de recursos Microsoft.KeyVault está registado. Para registar um fornecedor de recursos, aceda à subscrição utilizada para o recurso Azure Stack Edge. Aceda a **fornecedores de recursos,** procure *o Microsoft.KeyVault* e selecione e **registe-se.**</li> |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [resolver problemas com o dispositivo.](azure-stack-edge-gpu-troubleshoot.md)
