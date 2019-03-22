---
title: Elemento de interface do Usuário de FileUpload do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.FileUpload para o portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 92a5f7c058904015cb22a239b7e7c4938ae1fdae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895257"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.FileUpload
Um controlo que permite que um usuário especificar um ou mais ficheiros para carregar.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- `constraints.accept` Especifica os tipos de ficheiros que são mostrados na caixa de diálogo de arquivo do navegador. Consulte a [especificação do HTML5](https://www.w3.org/TR/html5/forms.html#attr-input-accept) para os valores permitidos. O valor predefinido é **nulo**.
- Se `options.multiple` está definido como **true**, o utilizador tem permissão para selecionar mais de um ficheiro na caixa de diálogo de arquivo do navegador. O valor predefinido é **false**.
- Este elemento suporta carregar arquivos em dois modos com base no valor de `options.uploadMode`. Se **ficheiro** for especificado, o resultado tem o conteúdo do ficheiro como um blob. Se **url** for especificada, em seguida, o ficheiro é carregado para uma localização temporária, e o resultado tem o URL do blob. Blobs temporários serão removidos após 24 horas. O valor predefinido é **ficheiro**.
- Um ficheiro carregado está protegido. O URL de saída inclui um [SAS token](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para acessar o arquivo durante a implementação.
- O valor de `options.openMode` determina como o arquivo é lido. Se o ficheiro deve ser texto simples, especifique **texto**; mais, especifique **binário**. O valor predefinido é **texto**.
- Se `options.uploadMode` está definido como **arquivo** e `options.openMode` está definida como **binário**, a saída é codificada em base64.
- `options.encoding` Especifica a codificação a utilizar ao ler o ficheiro. O valor predefinido é **UTF-8**e é utilizado apenas quando `options.openMode` está definido como **texto**.

## <a name="sample-output"></a>Resultado da amostra
Se options.multiple é false e options.uploadMode é o arquivo, o resultado tem o conteúdo do arquivo como uma cadeia de caracteres do JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se options.multiple for verdadeira and'options.uploadMode é o arquivo, em seguida, o resultado tem o conteúdo dos arquivos como uma matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se options.multiple é false e options.uploadMode é o url, em seguida, o resultado tem um URL como uma cadeia de caracteres do JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Se options.multiple é verdadeiro e options.uploadMode é o url, em seguida, a saída tem uma lista de URLs como uma matriz JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Ao testar um CreateUiDefinition, alguns navegadores (como o Google Chrome) truncam URLs geradas pelo elemento Microsoft.Common.FileUpload na consola do browser. Se pretender com o botão direito ligações individuais para copiar os URLs completos.


## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).
