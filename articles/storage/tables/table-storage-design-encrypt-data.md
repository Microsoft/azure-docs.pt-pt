---
title: Criptografe dados da tabela de armazenamento Azure [ Microsoft Docs
description: Saiba mais sobre encriptação de dados de tabela no armazenamento do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60326008"
---
# <a name="encrypt-table-data"></a>Encriptar dados de tabela
A Biblioteca de Clientes de Armazenamento .NET Azure suporta a encriptação de propriedades de entidades de cadeia para inserir e substituir operações. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta às cordas após a desencriptação.    

Para as tabelas, para além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito especificando um atributo [EncryptProperty] (para entidades POCO que derivam da TableEntity) ou uma encriptação resolver nas opções de pedido. Um resolver de encriptação é um delegado que pega uma chave de partição, chave de linha e nome de propriedade e devolve um Boolean que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente usa esta informação para decidir se encripta uma propriedade enquanto escreve ao fio. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar propriedade A; de outra forma encriptar propriedades A e B.) Não é necessário fornecer esta informação durante a leitura ou consulta de entidades.

## <a name="merge-support"></a>Suporte de fusão

A fusão não é atualmente apoiada. Como um subconjunto de propriedades pode ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resulta em perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente do serviço, ou usar uma nova chave por imóvel, ambas inadequadas por razões de desempenho.     

Para obter informações sobre encriptar dados de tabelas, consulte a encriptação do lado do cliente e o [Cofre de Chaves Azure para o Armazenamento Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de mesa](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
