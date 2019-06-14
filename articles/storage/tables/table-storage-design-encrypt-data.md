---
title: Encriptar dados da tabela de armazenamento do Azure | Documentos da Microsoft
description: Saiba mais sobre a encriptação de dados de tabela no armazenamento do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326008"
---
# <a name="encrypt-table-data"></a>Encriptar dados da tabela
Biblioteca de cliente de armazenamento do Azure de .NET suporta a encriptação das propriedades de entidade de cadeia de caracteres para inserir e substitua operações. As cadeias de caracteres encriptadas são armazenadas no serviço como propriedades binárias e elas são convertidas para cadeias de caracteres após desencriptação.    

Para tabelas, além da política de encriptação, os utilizadores tem de especificar as propriedades sejam encriptados. Isso pode ser feito, qualquer um dos especificando um atributo [EncryptProperty] (para entidades POCO que derivam de TableEntity) ou um resolvedor de encriptação nas opções de pedido. Um resolvedor de encriptação é um delegado que assume uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booleano que indica se essa propriedade deve ser encriptada. Durante a encriptação, a biblioteca de cliente utiliza estas informações para decidir se pretende encriptar uma propriedade ao escrever para a transmissão. O delegado também fornece a possibilidade de lógica em torno de como as propriedades são encriptadas. (Por exemplo, se X, em seguida, criptografar propriedades R; caso contrário, encriptar propriedades A e B.) Não é necessário fornecer estas informações ao ler ou consultar entidades.

## <a name="merge-support"></a>Suporte de intercalação

Merge não é atualmente suportada. Uma vez que um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente as novas propriedades de mesclagem e atualizar os metadados resulta em perda de dados. Intercalar o requer a fazer chamadas de serviço extra para ler a entidade já existente do serviço ou usando uma nova chave por propriedade, que não são adequados por motivos de desempenho.     

Para obter informações sobre a encriptação de dados da tabela, consulte [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Passos Seguintes

- [Padrões de Design de tabela](table-storage-design-patterns.md)
- [Modelando as relações](table-storage-design-modeling.md)
- [Modelando as relações](table-storage-design-modeling.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
