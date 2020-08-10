---
title: Encriptar dados da tabela de armazenamento do Azure Microsoft Docs
description: Saiba mais sobre encriptação de dados de tabela no armazenamento do Azure. A Biblioteca do Cliente de Armazenamento .NET Azure permite encriptar entidades de cadeias para inserir e substituir operações.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: fec213b9d7429714beb948f061445fd37d698624
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037495"
---
# <a name="encrypt-table-data"></a>Criptografe dados de tabela
A Biblioteca do Cliente de Armazenamento .NET Azure suporta a encriptação das propriedades da entidade de cordas para inserir e substituir operações. As cordas encriptadas são armazenadas no serviço como propriedades binárias, e são convertidas de volta em cordas após a desencriptação.    

Para as tabelas, além da política de encriptação, os utilizadores devem especificar as propriedades a encriptar. Isto pode ser feito especificando um atributo [EncryptProperty] (para entidades POCO que derivam da TableEntity) ou uma encriptação resolver nas opções de pedido. Um resolver de encriptação é um delegado que pega numa chave de partição, chave de linha e nome de propriedade e devolve um Boolean que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca do cliente utiliza esta informação para decidir se encripta uma propriedade enquanto escreve para o fio. O delegado também prevê a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, encriptar a propriedade A; caso contrário, encriptar propriedades A e B.) Não é necessário fornecer esta informação durante a leitura ou consulta de entidades.

## <a name="merge-support"></a>Suporte de fusão

A fusão não é suportada atualmente. Como um subconjunto de propriedades pode ter sido encriptado anteriormente usando uma chave diferente, simplesmente fundir as novas propriedades e atualizar os metadados resulta na perda de dados. A fusão requer fazer chamadas de serviço extra para ler a entidade pré-existente a partir do serviço, ou usar uma nova chave por propriedade, ambas as quais não são adequadas por razões de desempenho.     

Para obter informações sobre a encriptação de dados da tabela, consulte [a encriptação do lado do cliente e o cofre da chave Azure para o Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de mesa](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
