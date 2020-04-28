---
title: Elemento UI de FileUpload
description: Descreve o elemento Microsoft.Common.FileUpload UI para o portal Azure. Permite que os utilizadores precisem de carregar ficheiros ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652492"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI

Um controlo que permite ao utilizador especificar um ou mais ficheiros para fazer o upload.

## <a name="ui-sample"></a>Amostra de UI

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

## <a name="sample-output"></a>Resultado da amostra

Se as opções.multiple forem falsas e as opções.uploadMode é ficheiro, então a saída tem o conteúdo do ficheiro como uma cadeia JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se as opções.multiple forem verdadeiras e'options.uploadMode é ficheiro, então a saída tem o conteúdo dos ficheiros como uma matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se as opções.multiple forem falsas e as opções.uploadMode é url, então a saída tem um URL como uma cadeia JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Se as opções.multiple forem verdadeiras e as opções.uploadMode é url, então a saída tem uma lista de URLs como uma matriz JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Ao testar um CreateUiDefinition, alguns navegadores (como o Google Chrome) truncam URLs gerados pelo elemento Microsoft.Common.FileUpload na consola do navegador. Pode ser necessário clicar em links individuais para copiar os URLs completos.

## <a name="remarks"></a>Observações

- `constraints.accept`especifica os tipos de ficheiros que são mostrados no diálogo de ficheiros do navegador. Consulte a [especificação HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) para obter valores permitidos. O valor predefinido é **nulo.**
- Se `options.multiple` for definido como **verdadeiro,** o utilizador pode selecionar mais do que um ficheiro no diálogo de ficheiros do navegador. O valor predefinido é **falso**.
- Este elemento suporta o upload de ficheiros em `options.uploadMode`dois modos com base no valor de . Se o **ficheiro** for especificado, a saída tem o conteúdo do ficheiro como uma bolha. Se o **url** for especificado, o ficheiro é enviado para um local temporário, e a saída tem o URL da bolha. As bolhas temporárias serão purgadas após 24 horas. O valor predefinido é **ficheiro**.
- Um ficheiro carregado está protegido. O URL de saída inclui um [símbolo SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para aceder ao ficheiro durante a implementação.
- O valor `options.openMode` determina como o ficheiro é lido. Se se espera que o ficheiro seja texto simples, especifique **texto;** caso contrário, especificar **binário.** O valor predefinido é **texto**.
- Se `options.uploadMode` estiver **file** definido `options.openMode` para arquivar e estiver definido para **binário,** a saída é codificada por base64.
- `options.encoding`especifica a codificação a utilizar ao ler o ficheiro. O valor predefinido é **UTF-8** `options.openMode` , e é utilizado apenas quando está definido para **texto**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
