---
title: login de azcopy / Microsoft Docs
description: Este artigo fornece informações de referência para o comando de login da azcopia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8d2adca661882ea11d04ebe55afe25f7f9c2ef4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219955"
---
# <a name="azcopy-login"></a>azcopy login

Entra no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

## <a name="synopsis"></a>Sinopse

Faça login no Azure Ative Directory para aceder aos recursos de Armazenamento Azure.

Para ser autorizado na sua conta de Armazenamento Azure, tem de atribuir a função **de Contribuinte de Dados do Armazenamento à** sua conta de utilizador no contexto da conta de Armazenamento, grupo de recursos-mãe ou subscrição dos pais.

Este comando irá cache informações de login encriptadas para o utilizador atual utilizando os mecanismos incorporados do SISTEMA.

Consulte os exemplos para mais informações.

> [!IMPORTANT]
> Se definir uma variável ambiental utilizando a linha de comando, essa variável será legível no histórico da sua linha de comando. Considere limpar variáveis que contenham credenciais do histórico da sua linha de comando. Para evitar que as variáveis apareçam na sua história, pode utilizar um script para solicitar ao utilizador as suas credenciais e definir a variável ambiental.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceptuais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Dados de transferência com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com a AzCopy e armazenamento de ficheiros](storage-use-azcopy-files.md)
- [Configurar, otimizar e resolver problemas AzCopy](storage-use-azcopy-configure.md)

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

Faça login como diretor de serviço usando um segredo de cliente. Desenteie a variável ambiental AZCOPY_SPA_CLIENT_SECRET ao segredo do cliente para o serviço secreto auth.

```azcopy
azcopy login --service-principal
```

Faça login como um principal de serviço usando um certificado e senha. Desente a variável ambiental AZCOPY_SPA_CERT_PASSWORD à palavra-passe do certificado para autorização principal do serviço baseado em cert.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Certifique-se de tratar /caminho/para/meu/cert como um caminho para um ficheiro PEM ou PKCS12. A AzCopy não chega à loja de certificados do sistema para obter o seu certificado.

--o percurso de certificado é obrigatório quando se faz o serviço principal de cert auth.

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--aad-endpoint|O ponto final do Diretório Ativo Azure para utilizar. O `https://login.microsoftonline.com` predefinimento () é correto para a nuvem pública Azure. Desave este parâmetro ao autenticar numa nuvem nacional. Consulte [os pontos finais de autenticação AZURE AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Esta bandeira não é necessária para a Identidade de Serviço Gerido.|
|--cadeia de id aplicação|ID de aplicação de identidade atribuída ao utilizador. Obrigatório para o auth principal de serviço.|
|--cadeia de percurso de certificado|Caminho para certificado para autenticação SPN. Obrigatório para o auth principal de serviço baseado em certificado.|
|-h, -- ajuda|Mostrar conteúdo de ajuda para o comando de login.|
|--identidade|iniciar sessão utilizando a identidade da máquina virtual, também conhecida como identidade de serviço gerida (MSI).|
|--identidade-cliente-id cadeia|Identificação do cliente de identidade atribuída ao utilizador.|
|--identidade-id-id corda|ID de objeto de identidade atribuída ao utilizador.|
|--cadeia de identidade-id-id|Identificação de recursos da identidade atribuída pelo utilizador.|
|---serviço-principal|Faça login via SPN (Nome Principal de Serviço) utilizando um certificado ou um segredo. A palavra-passe secreta ou de certificado do cliente deve ser colocada na variável ambiente apropriada. Escreva `AzCopy env` para ver nomes e descrições de variáveis ambientais.|
|--corda de inquilino-id| o ID do inquilino de diretório ativo Azure para usar para o login interativo do dispositivo OAuth.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas dos comandos dos pais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Reduz a taxa de transferência, em megabits por segundo. A produção momentesa pode variar ligeiramente da tampa. Se esta opção for definida para zero, ou for omitida, a produção não está limitada.|
|--cadeia do tipo de saída|Formato da saída do comando. As escolhas incluem: texto, json. O valor predefinido é "texto".|
|--cadeia de sufixos de confiança da Microsoft-Sufixes   |Especifica sufixos de domínio adicionais onde podem ser enviados tokens de login do Azure Ative Directory.  O padrão é '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;* core.usgovcloudapi.net.' Qualquer listado aqui é adicionado ao padrão. Para a segurança, só deve colocar os domínios microsoft Azure aqui. Separe várias entradas com pontos e vírgulas.|

## <a name="see-also"></a>Veja também

- [azcopia](storage-ref-azcopy.md)
