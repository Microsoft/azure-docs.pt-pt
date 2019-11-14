---
title: logon do azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de logon azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e7998ea0753ba7ab5d97142c34dc9e333f8b4f5d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034083"
---
# <a name="azcopy-login"></a>azcopy login

Faz logon no Azure Active Directory para acessar os recursos de armazenamento do Azure.

## <a name="synopsis"></a>Resumo

Faça logon no Azure Active Directory para acessar os recursos de armazenamento do Azure.

Para ser autorizado à sua conta de armazenamento do Azure, você deve atribuir a função de **colaborador de dados do blob de armazenamento** à sua conta de usuário no contexto da conta de armazenamento, do grupo de recursos pai ou da assinatura pai.

Esse comando armazenará em cache as informações de logon criptografadas para o usuário atual usando os mecanismos internos do sistema operacional.

Consulte os exemplos para obter mais informações.

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no seu histórico de linha de comando. Considere limpar as variáveis que contêm credenciais do seu histórico de linha de comando. Para impedir que as variáveis apareçam no seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável de ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o armazenamento de arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Faça logon interativamente com a ID de locatário do AAD padrão definida como comum:

```azcopy
azcopy login
```

Fazer logon interativamente com uma ID de locatário especificada:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Faça logon usando a identidade atribuída pelo sistema de uma VM (máquina virtual):

```azcopy
azcopy login --identity
```

Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de cliente da identidade do serviço:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de objeto da identidade do serviço:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Faça logon usando a identidade atribuída pelo usuário de uma VM e uma ID de recurso da identidade do serviço:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Faça logon como uma entidade de serviço usando um segredo do cliente. Defina a variável de ambiente AZCOPY_SPA_CLIENT_SECRET como o segredo do cliente para autenticação de entidade de serviço baseada em segredo.

```azcopy
azcopy login --service-principal
```

Faça logon como uma entidade de serviço usando um certificado e uma senha. Defina a variável de ambiente AZCOPY_SPA_CERT_PASSWORD como a senha do certificado para a autorização de entidade de serviço baseada em certificado.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Certifique-se de tratar/Path/to/My/CERT como um caminho para um arquivo PEM ou PKCS12. AzCopy não chega ao repositório de certificados do sistema para obter seu certificado.

--o caminho do certificado é obrigatório ao fazer a autenticação de entidade de serviço baseada em certificado.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--ID do aplicativo-cadeia de caracteres|ID do aplicativo da identidade atribuída pelo usuário. Necessário para autenticação de entidade de serviço.|
|--Cadeia de caracteres de caminho do certificado|Caminho para o certificado para autenticação SPN. Necessário para autenticação de entidade de serviço baseada em certificado.|
|-h,--ajuda|Mostra o conteúdo da ajuda para o comando de logon.|
|--identidade|Faça logon usando a identidade da máquina virtual, também conhecida como MSI (identidade de serviço gerenciada).|
|--identidade-Client-ID cadeia de caracteres|ID do cliente da identidade atribuída pelo usuário.|
|--Identity-Object-ID String|ID de objeto da identidade atribuída pelo usuário.|
|--Identity-Resource-ID String|ID de recurso da identidade atribuída pelo usuário.|
|--entidade de serviço|Faça logon via SPN (nome da entidade de serviço) usando um certificado ou um segredo. O segredo do cliente ou a senha do certificado devem ser colocados na variável de ambiente apropriada. Digite `AzCopy env` para ver os nomes e descrições das variáveis de ambiente.|
|--Tenant-ID da cadeia| a ID de locatário do Azure Active Directory a ser usada para o logon interativo do dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
