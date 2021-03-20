---
title: Preservação de ACLs, atributos e timetamps com Caixa de Dados Azure
description: ACLs, timetamps e atributos preservados durante a cópia de dados via SMB para Azure Data Box. Copiar metadados com ferramentas de cópia de dados Windows e Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950317"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Preservação de ACLs, atributos e timetamps com Caixa de Dados Azure

A Azure Data Box permite-lhe preservar listas de controlo de acesso (ACLs), timetamps e atributos de ficheiros ao enviar dados para o Azure. Este artigo descreve os metadados que pode transferir ao copiar dados para a Caixa de Dados via Bloco de Mensagens do Servidor (SMB) para o fazer o upload para ficheiros Azure. 

São fornecidas etapas específicas para copiar metadados com ferramentas de cópia de dados Windows e Linux. Os metadados não são preservados ao transferir dados para o armazenamento de bolhas.

Neste artigo, os ACLs, os timetamps e os atributos de ficheiro que são transferidos são referidos colectivamente como *metadados.*

## <a name="transferred-metadata"></a>Metadados transferidos

Os metadados seguintes são transferidos quando os dados da Caixa de Dados são enviados para ficheiros Azure.

#### <a name="timestamps"></a>Carimbos de Data/Hora

São transferidos os seguintes picos de tempo:
- HoraDaCriação
- Última Hora da Morte

A seguinte hora não é transferida:
- LastAccessTime
  
#### <a name="file-attributes"></a>Atributos de arquivo

Os atributos de ficheiros em ambos os ficheiros e diretórios são transferidos, salvo indicação em contrário.

Os seguintes atributos de ficheiro são transferidos:
- FILE_ATTRIBUTE_READONLY (apenas arquivo)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (apenas diretório)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (apenas arquivo)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Os seguintes atributos de ficheiro não são transferidos:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Os atributos só de leitura nos diretórios não são transferidos.

#### <a name="acls"></a>ACLs

Todos os ACLs para diretórios e ficheiros que copia para a sua Caixa de Dados sobre SMB são copiados e transferidos. As transferências incluem acLs discricionários (DACLs) e ACLs de sistema (SACLs). Para o Linux, apenas os ACLs Windows NT são transferidos.

Os ACLs não são transferidos durante cópias de dados sobre o Sistema de Ficheiros de Rede (NTS) e quando utiliza o serviço de cópia de dados para transferir os seus dados. O serviço de cópia de dados lê dados diretamente das suas ações e não consegue ler ACLs.

Mesmo que a sua ferramenta de cópia de dados não copie ACLs, os ACLs predefinidos em diretórios e ficheiros são transferidos para ficheiros Azure. Os ACLs predefinidos têm permissões para a conta de Administrador incorporado, a conta SYSTEM e a conta de utilizador de partilha SMB que foi usada para montar e copiar dados na Caixa de Dados.

Os ACLs contêm descritores de segurança com as seguintes propriedades: ACLs, Proprietário, Grupo, SACL.

A transferência de ACLs é ativada por padrão. É melhor desativar esta definição na UI web local na sua Caixa de Dados. Para obter mais informações, consulte [a UI web local para administrar a sua Caixa de Dados e Caixa de Dados Pesada.](./data-box-local-web-ui-admin.md)

> [!NOTE]
> Os ficheiros com ACLs que contenham fios de acesso condicional (ACE) não são copiados. Este é um problema conhecido. Para contornar isto, copie estes ficheiros para os Ficheiros Azure partilhe manualmente montando a partilha e, em seguida, utilizando uma ferramenta de cópia que suporte a cópia de ACLs.

## <a name="copying-data-and-metadata"></a>Copiar dados e metadados

Para transferir os ACLs, timetamps e atributos para os seus dados, utilize os seguintes procedimentos para copiar dados para a Caixa de Dados. 

### <a name="windows-data-copy-tool"></a>Ferramenta de cópia de dados do Windows

Para copiar dados para a sua Caixa de Dados via SMB, utilize uma ferramenta de cópia de ficheiros compatível com SMB, como `robocopy` . O comando da amostra que se segue copia todos os ficheiros e diretórios, transferindo metadados juntamente com os dados.

Ao utilizar a `/copyall` ou `/dcopy:DAT` opção, certifique-se de que os privilégios necessários do Operador de Cópia de Segurança não estão desativados. Para obter mais informações, consulte [a UI web local para administrar a sua Caixa de Dados e Caixa de Dados Pesada.](./data-box-local-web-ui-admin.md) 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

em que

|Opção |Descrição |
|------------------- | ----- |
|`/copyall` |Copia todos os atributos.|
|`/e`      |Copia subdiretórios, incluindo diretórios vazios.         |
|`/dcopy:DAT`  |Copia dados, atributos e timetamps. Nota: A opção /dcopy:DAT deve ser utilizada para a transferência `CreationTime` em diretórios. |
|`/r:3`    |Especifica 3 recauchuções em cópias falhadas.         |
|`/w:60`   |Especifica um tempo de espera de 60 segundos entre as retrósias.         |
|`/is`     |Inclui os mesmos ficheiros.         |
|`/nfl`    |Não regista nomes de ficheiros.         |
|`/ndl`    |Não regista nomes de diretórios.        |
|`/np`     |Não apresenta progressos na operação de cópia.         |
|`/MT:32 or 64`  |Utiliza multi-leitura, com 32 ou 64 fios.           |
|`/fft`    |Reduz a granularidade do carimbo de tempo para qualquer sistema de ficheiros.        |
|`/log+:<LogFile>`  |Anexa a saída ao ficheiro de registo existente.|

Para obter mais informações sobre estes `robocopy` parâmetros, consulte [Tutorial: Copiar dados para Azure Data Box via SMB](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Ferramenta de cópia de dados Linux

Transferir metadados em Linux é um processo em duas etapas. Em primeiro lugar, copia os dados de origem utilizando uma ferramenta como `rsync` , que não copia metadados. Depois de copiar os dados, pode copiar os metadados utilizando uma ferramenta como `smbcacls` ou `cifsacl` . 

Os seguintes comandos de amostra fazem o primeiro passo, copiando os dados utilizando `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Passos seguintes

- [Copiar dados para o Azure Data Box via SMB](./data-box-deploy-copy-data.md)