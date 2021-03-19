---
title: Elemento UI de carga de arquivo
description: Descreve o elemento UI Microsoft.Common.FileUpload para o portal Azure. Permite que os utilizadores carreguem ficheiros ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87004290"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft.Common.FileUpload UI elemento

Um controlo que permite a um utilizador especificar um ou mais ficheiros para carregar.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

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

## <a name="sample-output"></a>Saída de exemplo

Se as opções.multiple são falsas e as opções.uploadMode é ficheiro, então a saída tem o conteúdo do ficheiro como uma cadeia JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se as opções.multiple forem verdadeiras e as opções.uploadMode forem ficheiros, então a saída tem o conteúdo dos ficheiros como uma matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se as opções.multiple são falsas e as opções.uploadMode é url, então a saída tem um URL como uma cadeia JSON:

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

Ao testar um CreateUiDefinition, alguns navegadores (como o Google Chrome) truncate URLs gerados pelo elemento Microsoft.Common.FileUpload na consola do navegador. Pode ser necessário clicar com o botão direito de links individuais para copiar os URLs completos.

## <a name="remarks"></a>Observações

- `constraints.accept` especifica os tipos de ficheiros que são mostrados no diálogo de ficheiros do navegador. Consulte a [especificação HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) para obter valores permitidos. O valor por defeito é **nulo.**
- Se `options.multiple` estiver definido como **verdadeiro,** o utilizador pode selecionar mais de um ficheiro no diálogo de ficheiros do navegador. O valor predefinido é **falso**.
- Este elemento suporta o upload de ficheiros em dois modos com base no valor de `options.uploadMode` . Se o **ficheiro** for especificado, a saída tem o conteúdo do ficheiro como uma bolha. Se **o url** for especificado, então o ficheiro é enviado para um local temporário, e a saída tem o URL da bolha. As bolhas temporárias serão purgadas após 24 horas. O valor predefinido é **ficheiro.**
- Um ficheiro carregado está protegido. O URL de saída inclui um [símbolo SAS para](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) aceder ao ficheiro durante a implementação.
- O valor determina `options.openMode` como o ficheiro é lido. Se se espera que o ficheiro seja texto simples, especifique **o texto;** caso contrário, especifique **binário.** O valor predefinido é **texto.**
- Se `options.uploadMode` estiver definido para **arquivar** e estiver definido `options.openMode` para **binário,** a saída é codificada com base64.
- `options.encoding` especifica a codificação a utilizar ao ler o ficheiro. O valor predefinido é **UTF-8**, e é utilizado apenas quando `options.openMode` é definido para **texto**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).
