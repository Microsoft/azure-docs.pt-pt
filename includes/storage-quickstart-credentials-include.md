---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7dd22886d11c3a35a7a866ff7c9a4f56ea74cab7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351233"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

Quando o aplicativo de exemplo faz uma solicitação para o armazenamento do Azure, ele deve ser autorizado. Para autorizar uma solicitação, adicione as credenciais da conta de armazenamento ao aplicativo como uma cadeia de conexão. Veja as credenciais da conta de armazenamento através dos seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, pode ver as chaves de acesso da conta e a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e selecione o botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente no Windows, você deve iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Depois de adicionar a variável de ambiente, reinicie os programas em execução que precisarão ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.
