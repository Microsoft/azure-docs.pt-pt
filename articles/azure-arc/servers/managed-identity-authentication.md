---
title: Autenticar contra recursos Azure com servidores ativados pela Arc
description: Este artigo descreve o suporte do Serviço de Metadados Azure Instance para servidores ativados pelo Arc e como pode autenticar contra os recursos do Azure e locais usando um segredo.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96939155"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Autenticar contra recursos Azure com servidores ativados pela Arc

Aplicações ou processos que executam diretamente num Azure Arc habilitado servidores podem aproveitar identidades geridas para aceder a outros recursos Azure que suportam a autenticação baseada no Diretório Azure. Uma aplicação pode obter um [token](../../active-directory/develop/developer-glossary.md#access-token) de acesso representando a sua identidade, que é atribuído ao sistema para servidores ativados arc, e usá-lo como um símbolo 'portador' para se autenticar para outro serviço.

Consulte a documentação geral de [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para uma descrição detalhada das identidades geridas, bem como a distinção entre identidades atribuídas ao sistema e identidades atribuídas pelo utilizador.

Neste artigo, mostramos como um servidor pode usar uma identidade gerida atribuída ao sistema para aceder ao Cofre [da Chave](../../key-vault/general/overview.md)Azure . Servindo como uma bota, o Key Vault permite que a aplicação do seu cliente utilize um segredo para aceder a recursos não garantidos pela Azure Ative Directory (AD). Por exemplo, os certificados TLS/SSL utilizados pelos seus servidores web IIS podem ser armazenados no Cofre da Chave Azure e implementar de forma segura os certificados para servidores Windows ou Linux fora do Azure.

## <a name="security-overview"></a>Descrição geral da segurança

Ao embarcar no seu servidor para servidores ativados Azure Arc, várias ações são realizadas para configurar usando uma identidade gerida, semelhante ao que é realizado para um Azure VM:

- O Azure Resource Manager recebe um pedido para ativar a identidade gerida atribuída pelo sistema no servidor ativado pelo Arco.

- O Azure Resource Manager cria um principal de serviço em Azure AD para a identidade do servidor. O principal de serviço é criado no inquilino do Azure AD no qual a subscrição confia.

- O Azure Resource Manager configura a identidade no servidor atualizando o ponto final de identidade do Serviço de Metadados de Instância Azure (IMDS) para [Windows](../../virtual-machines/windows/instance-metadata-service.md) ou [Linux](../../virtual-machines/linux/instance-metadata-service.md) com o ID e certificado principal do cliente de serviço. O ponto final é um ponto final REST acessível apenas a partir do servidor utilizando um endereço IP bem conhecido e não roteável. Este serviço fornece um subconjunto de informações de metadados sobre o servidor ativado pelo Arco para ajudar a geri-lo e configumentá-lo.

O ambiente de um servidor gerido com identidade será configurado com as seguintes variáveis num servidor ativado pelo Windows Arc:

- **IMDS_ENDPOINT**: O endereço IP do ponto final do IMDS `http://localhost:40342` para servidores ativados pelo Arc.

- **IDENTITY_ENDPOINT:** o ponto final local correspondente à identidade gerida do `http://localhost:40342/metadata/identity/oauth2/token` serviço.

O seu código que está a ser em execução no servidor pode solicitar um token a partir do ponto final do serviço Azure Instance Metadata, acessível apenas a partir do servidor.

A variável do ambiente do sistema **IDENTITY_ENDPOINT** é usada para descobrir o ponto final de identidade por aplicações. As aplicações devem tentar recuperar **valores IDENTITY_ENDPOINT** e **IMDS_ENDPOINT** e usá-los. As aplicações com qualquer nível de acesso são permitidas para fazer pedidos para os pontos finais. As respostas dos metadados são manuseadas normalmente e dadas a qualquer processo na máquina. No entanto, quando é feito um pedido que exporia um token, exigimos que o cliente forneça um segredo para atestar que eles são capazes de aceder a dados apenas disponíveis para utilizadores mais privilegiados.

## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão das identidades geridas.
- Um servidor ligado e registado com servidores ativados pelo Arc.
- É membro do [grupo Proprietário](../../role-based-access-control/built-in-roles.md#owner)** no grupo de subscrição ou recursos, de forma a executar as etapas necessárias de criação de recursos e gestão de funções.
- Um Cofre de Chaves Azure para armazenar e recuperar a sua credencial. e atribuir o acesso identitário do Arco Azure ao KeyVault.

    - Se não tiver um Cofre-Chave criado, consulte [Create Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-).
    - Para configurar o acesso pela identidade gerida utilizada pelo servidor, consulte o [acesso do Grant para Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) ou Grant access for [Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access). Para o passo número 5, vai introduzir o nome do servidor ativado pelo Arco. Para completar isto utilizando o PowerShell, consulte [Atribuir uma política de acesso utilizando o PowerShell](../../key-vault/general/assign-access-policy-powershell.md).

## <a name="acquiring-an-access-token-using-rest-api"></a>Aquisição de um token de acesso utilizando REST API

O método para obter e utilizar uma identidade gerida atribuída ao sistema para autenticar com recursos Azure é semelhante ao que é realizado com um Azure VM.

Para um servidor Windows ativado pelo Arc, utilizando o PowerShell, invoca o pedido web para obter o token do anfitrião local na porta específica. Especifique o pedido utilizando o endereço IP ou a variável ambiental **IDENTITY_ENDPOINT**.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

A resposta a seguir é um exemplo que é devolvido:

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="Uma recuperação bem sucedida do token de acesso usando o PowerShell.":::

Para um servidor Linux ativado pelo Arc, utilizando o Bash, você invoca o pedido web para obter o sinal do anfitrião local na porta específica. Especifique o seguinte pedido utilizando o endereço IP ou a variável ambiental **IDENTITY_ENDPOINT**. Para completar este passo, precisa de um cliente SSH.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

A resposta a seguir é um exemplo que é devolvido:

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Uma recuperação bem sucedida do símbolo de acesso usando Bash.":::

A resposta inclui o token de acesso que precisa para aceder a qualquer recurso em Azure. Para completar a configuração para autenticar para cofre de chave Azure, consulte [o Cofre da Chave de Acesso com Windows](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) ou Cofre de [Teclas de Acesso com Linux](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o Azure Key Vault, consulte [a visão geral do Key Vault](../../key-vault/general/overview.md).

- Saiba como atribuir um acesso de identidade gerido a um recurso utilizando o [PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) ou utilizando [o Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md).