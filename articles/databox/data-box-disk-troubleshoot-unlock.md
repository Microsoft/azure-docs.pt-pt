---
title: Azure Data Box Disk problemas de desbloqueio de problemas problemas problemas de desbloqueio de problemas Microsoft Docs
description: Descreve como resolver problemas no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148286"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Problemas de lançamento de discos em Disco de Caixa de Dados Azure

Este artigo aplica-se ao Microsoft Azure Data Box Disk e descreve os fluxos de trabalho utilizados para resolver quaisquer problemas ao utilizar a ferramenta de desbloqueio. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Erros da ferramenta de Desbloqueio do Data Box Disk


| Mensagem de erro/Comportamento da ferramenta      | Recomendações                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| A versão atual do .NET Framework não é suportada. As versões suportadas são a 4.5 e posteriores.<br><br>A ferramenta fecha com uma mensagem.  | O .NET 4.5 não está instalado. Instale o .NET 4.5 ou posterior no computador anfitrião que executa a ferramenta de desbloqueio do Data Box Disk.                                                                            |
| Não foi possível desbloquear ou verificar os volumes. Contacte o Suporte da Microsoft.  <br><br>A ferramenta não consegue desbloquear ou verificar qualquer unidade bloqueada. | A ferramenta não conseguiu desbloquear nenhuma das unidades desbloqueadas com a chave de acesso fornecida. Contacte o Suporte da Microsoft para saber quais os próximos passos.                                                |
| Os volumes seguintes são desbloqueados e verificados. <br>Letras de unidade do volume: E:<br>Não foi possível desbloquear nenhum volume com as chaves de acesso seguintes: werwerqomnf, qwerwerqwdfda <br><br>A ferramenta desbloqueia algumas unidades e lista as letras de unidade com êxito ou falhadas.| Êxito parcial. Não foi possível desbloquear algumas das unidades com a chave de acesso fornecida. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| Não foi possível encontrar volumes bloqueados. Certifique-se de que o disco que recebeu da Microsoft está corretamente ligado e no estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. As unidades já estão desbloqueadas ou não foram detetadas. Certifique-se de que as unidades estão ligadas e bloqueadas.                                                           |
| Erro fatal: parâmetro inválido<br>Nome do parâmetro: invalid_arg<br>UTILIZAÇÃO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Obtenha esta chave de acesso na encomenda do Azure DataBox Disk. A chave de acesso desbloqueia os discos.<br>/Help:           Esta opção fornece exemplos e ajuda na utilização do cmdlet.<br>/SystemCheck:    Esta opção verifica se o sistema cumpre os requisitos para executar a ferramenta.<br><br>Prima qualquer tecla para sair. | Foi introduzido um parâmetro inválido. Os únicos parâmetros permitidos são /SystemCheck, /PassKey e /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Desbloquear problemas para discos ao utilizar um cliente Windows

Esta secção detalha alguns dos principais problemas enfrentados durante a implementação do Disco de Caixa de Dados ao utilizar um cliente do Windows para cópia de dados.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problema: Não conseguiu desbloquear a unidade do BitLocker
 
**Motivo** 

Usou a palavra-passe no diálogo BitLocker e tentou desbloquear o disco através do diálogo de bloqueio bitLocker. Isto não ia funcionar.

**Resolução**

Para desbloquear os Discos da Caixa de Dados, é necessário utilizar a ferramenta Data Box Disk Unlock e fornecer a palavra-passe do portal Azure. Para mais informações, vá ao [Tutorial: Desfaça as malas, conecte e desbloqueie o Disco de Caixa](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)de Dados Azure .
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problema: Não conseguiu desbloquear ou verificar alguns volumes. Contacte o Suporte da Microsoft.
 
**Motivo**

Pode ver o seguinte erro no registo de erros e não conseguir desbloquear ou verificar alguns volumes.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Isto indica que provavelmente está a perder a versão apropriada do Windows PowerShell no seu cliente Windows.

**Resolução**

Pode instalar o [Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) e voltar a tentar a operação.
 
Se ainda não conseguir desbloquear os volumes, copie os registos da pasta que tem a ferramenta Data Box Disk Unlock e [contacte](data-box-disk-contact-microsoft-support.md)o Microsoft Support .

## <a name="next-steps"></a>Passos seguintes

- Saiba como resolver problemas de [validação.](data-box-disk-troubleshoot.md)
