---
title: Resolução de problemas do Azure Data Box Disk | Microsoft Docs
description: Descreve como resolver problemas no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 83b3a271006df38744b9de49ed6350bea3aeef4d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159388"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk"></a>Resolver problemas no disco do Azure Data Box

Este artigo aplica-se para o disco do Microsoft Azure Data Box e descreve os fluxos de trabalho utilizados para resolver quaisquer problemas que vê ao implementar esta solução. 

Este artigo inclui as secções seguintes:

- Transferir registos de diagnóstico
- Registos de atividades de consulta
- Erros da ferramenta de Desbloqueio do Data Box Disk
- Erros da ferramenta de Cópia Dividida do Data Box Disk

## <a name="download-diagnostic-logs"></a>Transferir registos de diagnóstico

Se ocorrerem erros durante o processo de cópia de dados, o portal apresenta um caminho para a pasta onde estão localizados os registos de diagnóstico. 

Os registos de diagnóstico podem ser:
- Registos de erros
- Registos verbosos  

Para navegar para o caminho do registo de cópia, vá para a conta de armazenamento associada à sua encomenda do Data Box. 

1.  Aceda a **Geral > Detalhes da encomenda** e anote a conta de armazenamento associada à sua encomenda.
 

2.  Aceda a **Todos os recursos** e procure a conta de armazenamento identificada no passo anterior. Selecione e clique na conta de armazenamento.

    ![Copiar registos 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Aceda a **Serviço Blob > Procurar blobs** e procure o blob correspondente à conta de armazenamento. Aceda a **diagnosticslogcontainer > waies**. 

    ![Copiar registos 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Deverá ver os registos de erros e os registos verbosos da cópia de dados. Selecione e clique em cada ficheiro e, em seguida, transfira uma cópia local.

## <a name="query-activity-logs"></a>Registos de Atividades de Consulta

Utilize os registos de atividades para encontrar um erro quando resolver um problema ou para monitorizar a forma como um utilizador na sua organização alterou um recurso. Através dos registos de atividades, pode determinar:

- As operações que foram executadas nos recursos na sua subscrição.
- Quem iniciou a operação. 
- Quando ocorreu a operação.
- O estado da operação.
- Os valores de outras propriedades que possam ajudá-lo a procurar a operação.

O registo de atividades contém todas as operações de escrita (como PUT, POST e DELETE) efetuadas nos seus recursos, mas não inclui as operações de leitura (como GET). 

Os registos de atividades são mantidos durante 90 dias. Pode consultar qualquer intervalo de datas, desde que a data de início não seja superior a 90 dias no passado. Também pode filtrar por uma das consultas incorporadas no Insights. Por exemplo, clique no erro e, em seguida, selecione e clique nas falhas específicas para compreender a causa raiz.

## <a name="data-box-disk-unlock-tool-errors"></a>Erros da ferramenta de Desbloqueio do Data Box Disk


| Mensagem de erro/Comportamento da ferramenta      | Recomendações                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Nenhuma<br><br>A ferramenta de desbloqueio do Data Box Disk falha.                                                                            | O BitLocker não está instalado. Certifique-se de que o computador anfitrião que está a executar a ferramenta de desbloqueio do Data Box Disk tem o BitLocker instalado.                                                                            |
| A versão atual do .NET Framework não é suportada. As versões suportadas são a 4.5 e posteriores.<br><br>A ferramenta fecha com uma mensagem.  | O .NET 4.5 não está instalado. Instale o .NET 4.5 ou posterior no computador anfitrião que executa a ferramenta de desbloqueio do Data Box Disk.                                                                            |
| Não foi possível desbloquear ou verificar os volumes. Contacte o Suporte da Microsoft.  <br><br>A ferramenta não consegue desbloquear ou verificar qualquer unidade bloqueada. | A ferramenta não conseguiu desbloquear nenhuma das unidades desbloqueadas com a chave de acesso fornecida. Contacte o Suporte da Microsoft para saber quais os próximos passos.                                                |
| Os volumes seguintes são desbloqueados e verificados. <br>Letras de unidade do volume: E:<br>Não foi possível desbloquear nenhum volume com as chaves de acesso seguintes: werwerqomnf, qwerwerqwdfda <br><br>A ferramenta desbloqueia algumas unidades e lista as letras de unidade com êxito ou falhadas.| Êxito parcial. Não foi possível desbloquear algumas das unidades com a chave de acesso fornecida. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| Não foi possível encontrar volumes bloqueados. Certifique-se de que o disco que recebeu da Microsoft está corretamente ligado e no estado bloqueado.          | A ferramenta não consegue encontrar unidades bloqueadas. As unidades já estão desbloqueadas ou não foram detetadas. Certifique-se de que as unidades estão ligadas e bloqueadas.                                                           |
| Erro fatal: Parâmetro inválido<br>Nome do parâmetro: invalid_arg<br>UTILIZAÇÃO:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Exemplo: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Exemplo: DataBoxDiskUnlock /SystemCheck<br>Exemplo: DataBoxDiskUnlock /Help<br><br>/ Chaves de acesso:       Obter esta chave de acesso da encomenda do disco do Azure data box. A chave de acesso desbloqueia os discos.<br>/ Ajuda:           Esta opção fornece ajuda sobre a utilização do cmdlet e exemplos.<br>/ SystemCheck:    Esta opção verifica se o seu sistema cumpre os requisitos para executar a ferramenta.<br><br>Prima qualquer tecla para sair. | Foi introduzido um parâmetro inválido. Os únicos parâmetros permitidos são /SystemCheck, /PassKey e /Help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Erros da ferramenta de Cópia Dividida do Data Box Disk

|Mensagem de erro/avisos  |Recomendações |
|---------|---------|
|[Informações] A obter a palavra-passe de bitlocker para o volume: m <br>[Erro] Detetada exceção ao obter a chave de bitlocker para o volume m:<br> A sequência não contém elementos.|Este erro é apresentado se o Data Box Disk de destino estiver offline. <br> Utilize a ferramenta `diskmgmt.msc` para os discos online.|
|[Erro] Excepção emitida: Falha na operação de WMI:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = o formato da palavra-passe de recuperação fornecida é inválido. <br>As palavras-passe de recuperação do BitLocker têm 48 dígitos. <br>Certifique-se de que a palavra-passe de recuperação está no formato correto e, em seguida, tente novamente.|Utilize a ferramenta de Desbloqueio do Data Box Disk para desbloquear primeiro os discos e repita o comando. Para obter mais informações, aceda a <li> [Desbloquear o Data Box Disk para clientes Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Desbloquear o Data Box Disk para clientes Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Erro] Excepção emitida: Existe um ficheiro de DriveManifest.xml na unidade de destino. <br> Isto indica que a unidade alvo pode ter sido preparada com um ficheiro de diário diferente. <br>Para adicionar mais dados à mesma unidade, utilize o ficheiro de diário anterior. Para eliminar os dados existentes e reutilizar a unidade alvo para uma nova tarefa de importação, elimine o DriveManifest.xml da unidade. Execute novamente este comando com um novo ficheiro de diário.| Este erro é apresentado quando tenta utilizar o mesmo conjunto de unidades para várias sessões de importação. <br> Utilize um conjunto de unidades para uma só sessão de divisão e cópia.|
|[Erro] Excepção emitida: CopySessionId importdata-Setembro-teste-1 se refere a uma sessão de cópia anterior e não pode ser reutilizado para uma nova sessão de cópia.|Este erro é reportado quando tenta utilizar o mesmo nome de tarefa para uma nova tarefa como sendo uma tarefa anterior concluída com êxito.<br> Atribua um nome exclusivo à nova tarefa.|
|[Informações] O nome do ficheiro ou diretório de destino excede o limite de comprimento NTFS. |Esta mensagem é reportada quando o ficheiro de destino tiver sido renomeado devido ao caminho de ficheiro longo.<br> Modifique a opção de disposição no ficheiro `config.json` para controlar este comportamento.|
|[Erro] Excepção emitida: Sequência de escape JSON inválida. |Esta mensagem é reportada quando o config.json está num formato inválido. <br> Valide o `config.json` com [JSONlint](https://jsonlint.com/) antes de guardar o ficheiro.|



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir o Data Box Disk através do portal do Azure](data-box-portal-ui-admin.md).
