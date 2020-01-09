---
title: Elemento de interface do usuário FileUpload
description: Descreve o elemento Microsoft. Common. FileUpload da interface do usuário para portal do Azure. Permite que os usuários carreguem arquivos ao implantar um aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652492"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elemento de interface do usuário Microsoft. Common. FileUpload

Um controle que permite que um usuário especifique um ou mais arquivos a serem carregados.

## <a name="ui-sample"></a>Exemplo de interface do usuário

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

Se options. Multiple for false e Options. uploadmode for File, a saída terá o conteúdo do arquivo como uma cadeia de caracteres JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Se options. Multiple for true and'options. uploadmode for File, a saída terá o conteúdo dos arquivos como uma matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Se options. Multiple for false e Options. uploadmode for URL, a saída terá uma URL como uma cadeia de caracteres JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Se options. Multiple for true e Options. uploadmode for URL, a saída terá uma lista de URLs como uma matriz JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Ao testar um CreateUiDefinition, alguns navegadores (como Google Chrome) truncam URLs geradas pelo elemento Microsoft. Common. FileUpload no console do navegador. Talvez seja necessário clicar com o botão direito do mouse em links individuais para copiar as URLs completas.

## <a name="remarks"></a>Observações

- `constraints.accept` especifica os tipos de arquivos que são mostrados na caixa de diálogo arquivo do navegador. Consulte a [especificação do HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) para obter os valores permitidos. O valor padrão é **NULL**.
- Se `options.multiple` for definido como **true**, o usuário poderá selecionar mais de um arquivo na caixa de diálogo de arquivo do navegador. O valor predefinido é **false**.
- Esse elemento dá suporte ao carregamento de arquivos em dois modos com base no valor de `options.uploadMode`. Se o **arquivo** for especificado, a saída terá o conteúdo do arquivo como um blob. Se a **URL** for especificada, o arquivo será carregado em um local temporário e a saída terá a URL do blob. Os BLOBs temporários serão limpos após 24 horas. O valor padrão é **File**.
- Um arquivo carregado está protegido. A URL de saída inclui um [token SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para acessar o arquivo durante a implantação.
- O valor de `options.openMode` determina como o arquivo é lido. Se for esperado que o arquivo seja de texto sem formatação, especifique o **texto**; caso contrário, especifique **Binary**. O valor padrão é **Text**.
- Se `options.uploadMode` for definido como **File** e `options.openMode` for definido como **Binary**, a saída será codificada em base64.
- `options.encoding` especifica a codificação a ser usada ao ler o arquivo. O valor padrão é **UTF-8**e é usado somente quando `options.openMode` é definido como **Text**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).
