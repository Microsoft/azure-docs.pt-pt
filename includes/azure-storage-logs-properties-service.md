---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6d90b4b68f1f9f4a0fcd20c1d371a32759449fab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711391"
---
| Propriedade | Descrição |
|:--- |:---|
|**contaName** | O nome da conta de armazenamento. Por exemplo: `mystorageaccount`.  |
|**requestUrl** | A URL que é solicitada. |
|**userAgentHeader** | O valor **do cabeçalho do agente de utilizador,** em aspas. Por exemplo: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**remetedorHeader** | O valor do cabeçalho **do remetente.** Por exemplo: `http://contoso.com/about.html`.|
|**clienteRequestId** | O valor do cabeçalho **x-ms-cliente-pedido-id** do pedido. Por exemplo: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | O identificador ETag para o objeto devolvido, em aspas. Por exemplo: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | O tempo total expresso em milissegundos para realizar a operação solicitada. Este valor não inclui a latência da rede (o momento para ler o pedido de entrada e enviar a resposta ao solicitador). Por exemplo: `22`. |
|**tipo de serviço** | O serviço associado a este pedido. Por exemplo: `blob` , , ou . `table` `files` `queue` . |
|**operaçãoContagem** | O número de cada operação registada que está envolvida no pedido. Esta contagem começa com um índice de `0` . Alguns pedidos requerem mais do que uma operação. A maioria dos pedidos realiza apenas uma operação. Por exemplo: `1`. |
|**pedidoSssizem** | O tamanho do cabeçalho de pedido expresso em bytes. Por exemplo: `578`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio. |
|**requestBodySize** | A dimensão dos pacotes de pedido, expressos em bytes, que são lidos pelo serviço de armazenamento. <br> Por exemplo: `0`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio.  |
|**respostaHeaderSize** | O tamanho do cabeçalho de resposta expresso em bytes. Por exemplo: `216`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio.  |
|**respostaCorpsize** | O tamanho dos pacotes de resposta escritos pelo serviço de armazenamento, em bytes. Se um pedido não for bem sucedido, este valor pode estar vazio. Por exemplo: `216`.  |
|**solicitaçãoMd5** | O valor do cabeçalho **Content-MD5** ou do cabeçalho **x-ms-conteúdo-md5** no pedido. O valor de haxixe MD5 especificado neste campo representa o conteúdo do pedido. Por exemplo: `788815fd0198be0d275ad329cafd1830`. <br>Este campo pode estar vazio.  |
|**servidorMd5** | O valor do haxixe MD5 calculado pelo serviço de armazenamento. Por exemplo: `3228b3cf1069a5489b298446321f8521`. <br>Este campo pode estar vazio.  |
|**última Hora DaModified** | O último tempo modificado (LMT) para o objeto devolvido.  Por exemplo: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Este campo está vazio para operações que podem devolver vários objetos. |
|**condiçõesUs** | Uma lista separada de pares de valores-chave que representam uma condição. As condições podem ser qualquer uma das seguintes: <li> If-Modificado-Desde <li> Se-Não modificada-Desde <li> If-Match <li> Se-Nenhum-Match  <br> Por exemplo: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | O valor do cabeçalho content-length para o pedido enviado para o serviço de armazenamento. Se o pedido tiver sido bem sucedido, este valor é igual ao pedido DoCorp. Se um pedido não for bem sucedido, este valor pode não ser igual ao pedido Dosize, ou pode estar vazio. |
|**tlsVersion** | A versão TLS utilizada no âmbito do pedido. Por exemplo: `TLS 1.2`. |
|**smbTreeConnectID** | A árvore do Bloco de Mensagens do Servidor (SMB) **ConnectId** estabelecida na hora de ligação à árvore. Por exemplo: `0x3` |
|**smbPersistentHandleID** | ID de manípulo persistente de um pedido SMB2 CREATE que sobrevive a reconectações de rede.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID. Persistente**. Por exemplo: `0x6003f` |
|**smbVolatileHandleID** | ID de cabo volátil a partir de um pedido SMB2 CREATE que é reciclado em redesenectações de rede.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID. Volátil.** Por exemplo: `0xFFFFFFFF00000065` |
|**smbMessageID** | A ligação relativa **MessageId**. Por exemplo: `0x3b165` |
|**smbCreditsConsumed** | A entrada ou saída consumida pelo pedido, em unidades de 64k. Por exemplo: `0x3` |
|**smbCommandDetail** | Mais informações sobre este pedido específico e não sobre o tipo geral de pedido. Por exemplo: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | O **FileId** associado ao ficheiro ou diretório.  Aproximadamente análogo a um NTFS FileId. Por exemplo: `0x9223442405598953` |
|**smbSessionID** | O **SMSM2 SessionId** foi estabelecido na hora de configuração da sessão. Por exemplo: `0x8530280128000049` |
|**smbCommandMajor uint32** | Valor no **SMB2_HEADER.Comando**. Atualmente, este é um número entre 0 e 18 inclusive. Por exemplo: `0x6` |
|**smbCommandMinor** | A subclasse de **SmbCommandMajor,** se for caso disso. Por exemplo: `DirectoryCloseAndDelete` |