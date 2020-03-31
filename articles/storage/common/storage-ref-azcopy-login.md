---
title: login azcopy [ Microsoft Docs
description: Este artigo fornece informações de referência para o comando de login da azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295411"
---
# <a name="azcopy-login"></a>azcopy login

Inicia sessão no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

## <a name="synopsis"></a>Sinopse

Inicie sessão no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

Para ser autorizado na sua conta de Armazenamento Azure, deve atribuir a função de Colaborador de Dados do **Depósito Blob** à sua conta de utilizador no contexto da conta de Armazenamento, do grupo de recursos-mãe ou da subscrição dos pais.

Este comando irá cache informações de login encriptadas para o utilizador atual utilizando os mecanismos incorporados do SISTEMA.

Consulte os exemplos para mais informações.

> [!IMPORTANT]
> Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configure, otimize e problemas AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Faça login interactivamente com o id de inquilino padrão da AAD definido para comum:

```azcopy
azcopy login
```

Faça login interactivamente com um ID de inquilino especificado:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Faça login utilizando a identidade atribuída pelo sistema de uma máquina virtual (VM):

```azcopy
azcopy login --identity
```

Faça login utilizando a identidade atribuída ao utilizador de um VM e um ID do cliente da identidade de serviço:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Faça login utilizando a identidade atribuída ao utilizador de um VM e um Id do Objeto da identidade de serviço:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Faça login utilizando a identidade atribuída ao utilizador de um VM e um Id de recurso da identidade de serviço:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Faça login como diretor de serviço usando um segredo de cliente. Desloque a variável ambiental AZCOPY_SPA_CLIENT_SECRET ao segredo do cliente para o principal auth de serviço baseado em segredo.

```azcopy
azcopy login --service-principal
```

Faça login como diretor de serviço utilizando um certificado e senha. Deteto a variável ambiental AZCOPY_SPA_CERT_PASSWORD à palavra-passe do certificado para a autorização principal do serviço baseado em CERT.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Certifique-se de tratar /path/to/my/cert como um caminho para um ficheiro PEM ou PKCS12. A AzCopy não chega à loja cert do sistema para obter o seu certificado.

-certificado-caminho é obrigatório quando se faz auth principal de serviço cert-based.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--aad-endpoint|O ponto final do Diretório Ativo Azure a utilizar. O padrão`https://login.microsoftonline.com`é correto para a nuvem pública de Azure. Defina este parâmetro ao autenticar numa nuvem nacional. Consulte [os pontos finais de autenticação Da Azure.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)
Esta bandeira não é necessária para a Identidade de Serviço Gerida.|
|--cadeia de aplicação-id|Id de aplicação da identidade atribuída ao utilizador. Necessário para o auth principal de serviço.|
|--cadeia de caminho-de-certificado|Caminho para certificado para autenticação SPN. Necessário para o auth principal de serviço baseado em certificado.|
|-h, --ajuda|Mostre o conteúdo da ajuda para o comando de login.|
|--identidade|log in usando a identidade da máquina virtual, também conhecida como identidade de serviço gerida (MSI).|
|--identidade-cliente-id string|Identificação do cliente da identidade atribuída ao utilizador.|
|--identidade-objeto-id string|Identificação do objeto da identidade atribuída ao utilizador.|
|--cadeia de identidade-recurso-id|Identificação de recursos da identidade atribuída ao utilizador.|
|--serviço-principal|Faça login via SPN (Nome Principal de Serviço) utilizando um certificado ou um segredo. O segredo do cliente ou a palavra-passe do certificado devem ser colocados na variável ambiente adequada. Digite `AzCopy env` para ver nomes e descrições de variáveis ambientais.|
|--cadeia de inquilino-id| o ID de inquilino de diretório ativo Azure para usar para login interativo dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Cobre a taxa de transferência, em megabits por segundo. A entrada momentânea pode variar ligeiramente a partir da tampa. Se esta opção estiver definida para zero, ou for omitida, a entrada não está limitada.|
|--cadeia tipo saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
