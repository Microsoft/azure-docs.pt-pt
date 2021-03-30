---
title: Encriptar os dados da tabela de armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre encriptação de dados de tabela no armazenamento do Azure. A Biblioteca do Cliente de Armazenamento .NET Azure permite encriptar entidades de cadeias para inserir e substituir operações.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.subservice: tables
ms.openlocfilehash: b921be718bfeb5eb95d4a802fb4d2a8cdd0946c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236782"
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
