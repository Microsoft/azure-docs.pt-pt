---
title: Azure Data Box Disk resolução de problemas de desbloqueio de problemas de desbloqueio de problemas Microsoft Docs
description: Saiba mais sobre os fluxos de trabalho para resolver problemas para a ferramenta desbloqueio com disco de caixa de dados Azure. Consulte os erros da ferramenta De desbloqueio de caixa de dados.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87925566"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Resolução de problemas de desbloqueio de discos no disco da caixa de dados do Azure

Este artigo aplica-se ao Disco de Caixa de Dados do Microsoft Azure e descreve os fluxos de trabalho utilizados para resolver problemas ao utilizar a ferramenta desbloqueio. 


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
| Não foi possível encontrar volumes bloqueados. Certifique-se de que o disco que recebeu da Microsoft está corretamente ligado e no estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. As unidades já estão desbloqueadas ou não foram detetadas. Certifique-se de que as unidades estão ligadas e bloqueadas. <br> <br>Pode também ver este erro se tiver formatado os discos. Se formaram os discos, estes são agora inutilizáveis. Contacte o Suporte da Microsoft para saber quais os próximos passos.                                                          |
| Erro fatal: parâmetro inválido<br>Nome do parâmetro: invalid_arg<br>UTILIZAÇÃO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Obtenha esta chave de acesso na encomenda do Azure DataBox Disk. A chave de acesso desbloqueia os discos.<br>/Help:           Esta opção fornece exemplos e ajuda na utilização do cmdlet.<br>/SystemCheck:    Esta opção verifica se o sistema cumpre os requisitos para executar a ferramenta.<br><br>Prima qualquer tecla para sair. | Foi introduzido um parâmetro inválido. Os únicos parâmetros permitidos são /SystemCheck, /PassKey, e /Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Desbloqueie problemas para discos ao utilizar um cliente Windows

Esta secção detalha alguns dos principais problemas enfrentados durante a implementação do Disco caixa de dados quando se utiliza um cliente Windows para cópia de dados.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problema: Não foi possível desbloquear a unidade do BitLocker
 
**Motivo** 

Utilizou a palavra-passe no diálogo BitLocker e tentou desbloquear o disco através do diálogo de desbloqueio bitLocker. Isto não ia funcionar.

**Resolução**

Para desbloquear os Discos de Caixa de Dados, tem de utilizar a ferramenta Desbloqueio de Disco de Caixa de Dados e fornecer a palavra-passe a partir do portal Azure. Para mais informações, aceda ao [Tutorial: Desembale, ligue e desbloqueie o disco Azure Data Box](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problema: Não foi possível desbloquear ou verificar alguns volumes. Contacte o Suporte da Microsoft.
 
**Motivo**

Pode ver o seguinte erro no registo de erro e não é capaz de desbloquear ou verificar alguns volumes.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Isto indica que provavelmente está a perder a versão apropriada do Windows PowerShell no seu cliente Windows.

**Resolução**

Pode instalar [o Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) e voltar a tentar a operação.
 
Se ainda não conseguir desbloquear os volumes, copie os registos da pasta que tem a ferramenta Data Box Disk Unlock e contacte o [Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [resolver problemas de validação](data-box-disk-troubleshoot.md).
