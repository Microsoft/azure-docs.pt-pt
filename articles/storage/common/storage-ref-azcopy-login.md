---
title: '| de login de azcopia Microsoft Docs'
description: Este artigo fornece informações de referência para o comando de login da azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503240"
---
# <a name="azcopy-login"></a>azcopy login

Entra no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

## <a name="synopsis"></a>Sinopse

Faça login no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

Para ser autorizado na sua conta de Armazenamento Azure, tem de atribuir a função **de Contribuinte de Dados do Armazenamento à** sua conta de utilizador no contexto da conta de Armazenamento, do grupo de recursos-mãe ou da subscrição dos pais.

Este comando irá cache informações de login encriptadas para o utilizador atual utilizando os mecanismos incorporados do SISTEMA.

> [!IMPORTANT]
> Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Tutorial: Migrar dados no local para armazenamento em nuvem com AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Dados de transferência com armazenamento AzCopy e Blob](./storage-use-azcopy-v10.md#transfer-data)
- [Transferir dados com o AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)

## <a name="examples"></a>Exemplos

Faça login interativamente com iD de inquilino AAD padrão definido para comum:

```azcopy
azcopy login
```

Faça login interativamente com uma iD de inquilino especificado:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Iniciar sessão utilizando a identidade atribuída ao sistema de uma Máquina Virtual (VM):

```azcopy
azcopy login --identity
```

Faça login utilizando a identidade atribuída ao utilizador de um VM e um ID do Cliente da identidade do serviço:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Faça login utilizando a identidade atribuída pelo utilizador de um VM e um ID de objeto da identidade do serviço:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Faça login utilizando a identidade atribuída pelo utilizador de um VM e um ID de recursos da identidade do serviço:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Faça login como principal de serviço usando um segredo de cliente: Desementa a variável ambiental AZCOPY_SPA_CLIENT_SECRET ao segredo do cliente para o serviço secreto auth.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Faça login como um principal de serviço usando um certificado e sua senha:

Desaprote a variável ambiental AZCOPY_SPA_CERT_PASSWORD à palavra-passe do certificado para o serviço principal baseado em cert:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Trate `/path/to/my/cert` como um caminho para um ficheiro PEM ou PKCS12. A AzCopy não chega à loja de certificados do sistema para obter o seu certificado.

`--certificate-path` é obrigatório quando se faz o serviço principal auth.

## <a name="options"></a>Opções

**--aad-endpoint** string O ponto final do Azure Ative Directory para usar. O padrão https://login.microsoftonline.com) (é correto para a nuvem Azure global. Desave este parâmetro ao autenticar numa nuvem nacional. Não é necessário para a identidade de serviço gerido.

**...-id de aplicação id** ID ID de identidade atribuída ao utilizador. Obrigatório para o auth principal de serviço.

**--percurso de cadeia de certificados** Caminho para certificado para autenticação SPN. Obrigatório para o auth principal de serviço baseado em certificado.

**...ajuda**   para o `azcopy login` comando.

**--identidade**   Faça login utilizando a identidade da máquina virtual, também conhecida como identidade de serviço gerida (MSI).

**...-identidade-cliente-id** ID identificação do cliente de identidade atribuída ao utilizador.

**--ID** de id de identificação de identificação de identificação atribuída pelo utilizador.

**--ID de id** de id de identidade de identidade atribuída pelo utilizador.

**---serviço-principal**   Faça login através do Nome Principal de Serviço (SPN) utilizando um certificado ou um segredo. A palavra-passe secreta ou de certificado do cliente deve ser colocada na variável ambiente apropriada. Digite AzCopy env para ver nomes e descrições de variáveis ambientais.

**--** cadeia de id inquilino O ID do inquilino Azure Ative Directory para usar para o login interativo do dispositivo OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|---cap-mbps flutuar|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Ver também

- [azcopia](storage-ref-azcopy.md)
