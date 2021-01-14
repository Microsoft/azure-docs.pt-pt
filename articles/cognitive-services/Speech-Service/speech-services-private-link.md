---
title: Como usar pontos finais privados com serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar os Serviços de Fala com pontos finais privados fornecidos pela Azure Private Link
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 61be4b45df94c902c0473b94a6dd83237c72da3c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196129"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Use serviços de fala através de um ponto final privado

[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe ligar-se aos serviços em Azure utilizando um [ponto final privado.](../../private-link/private-endpoint-overview.md) Um ponto final privado é um endereço IP privado que é acessível apenas dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e sub-rede.

Este artigo explica como configurar e usar o Private Link e os pontos finais privados com serviços de fala nos Serviços Cognitivos Azure.

> [!NOTE]
> Antes de prosseguir, [reveja como utilizar redes virtuais com Serviços Cognitivos.](../cognitive-services-virtual-networks.md)

Este artigo também descreve [como remover pontos finais privados mais tarde, mas ainda usar o recurso Discurso](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Crie um nome de domínio personalizado

Os pontos finais privados requerem um [nome de subdomínio personalizado para serviços cognitivos.](../cognitive-services-custom-subdomains.md) Utilize as seguintes instruções para criar uma para o seu recurso Speech.

> [!WARNING]
> Um recurso de fala com um nome de domínio personalizado habilitado usa uma forma diferente de interagir com os Serviços de Fala. Poderá ter de ajustar o seu código de aplicação para ambos os cenários: [ponto final privado ativado](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) e [ *não* privado ativado](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Quando ativa um nome de domínio personalizado, a operação não é [reversível](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). A única maneira de voltar ao [nome regional](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) é criar um novo recurso de Discurso.
>
> Se o seu recurso Speech tiver muitos modelos e projetos personalizados associados criados através [do Speech Studio,](https://speech.microsoft.com/)recomendamos vivamente experimentar a configuração com um recurso de teste antes de modificar o recurso utilizado na produção.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para criar um nome de domínio personalizado utilizando o portal Azure, siga estes passos:

1. Aceda ao [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.
1. Selecione o recurso de discurso necessário.
1. No grupo **de Gestão de Recursos** no painel esquerdo, selecione **Networking**.
1. No separador **Firewalls e redes virtuais,** selecione **Gerar Nome de Domínio Personalizado**. Um novo painel direito aparece com instruções para criar um subdomínio personalizado único para o seu recurso.
1. No painel **'Gerar** nome de domínio personalizado', insira um nome de domínio personalizado. Todo o seu domínio personalizado será como: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Lembre-se que depois de criar um nome de domínio personalizado, _não pode_ ser alterado.
    
    Depois de introduzir o seu nome de domínio personalizado, **selecione Save**.
1. Após o fim da operação, no grupo **de gestão de recursos,** selecione **Keys e Endpoint**. Confirme se o novo nome do ponto final do seu recurso começa desta forma: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um nome de domínio personalizado utilizando o PowerShell, confirme que o seu computador tem a versão 7.x do PowerShell ou mais tarde com a versão 5.1.0 ou posterior do módulo Azure PowerShell. Para ver as versões destas ferramentas, siga estes passos:

1. Numa janela PowerShell, insira:

    `$PSVersionTable`

    Confirme que o `PSVersion` valor é 7.x ou mais tarde. Para atualizar o PowerShell, siga as instruções na [Instalação de várias versões do PowerShell](/powershell/scripting/install/installing-powershell).

1. Numa janela PowerShell, insira:

    `Get-Module -ListAvailable Az`

    Se nada aparecer, ou se a versão do módulo Azure PowerShell for superior a 5.1.0, siga as instruções no [Install the Azure PowerShell](/powershell/azure/install-Az-ps) para atualizar.

Antes de prosseguir, corra `Connect-AzAccount` para criar uma ligação com Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Verifique se um nome de domínio personalizado está disponível

Verifique se o domínio personalizado que pretende utilizar está disponível. O seguinte código confirma que o domínio está disponível utilizando a operação [de disponibilidade de domínio de verificação](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) na API dos Serviços Cognitivos.

> [!TIP]
> O seguinte código *não* funcionará em Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Se o nome desejado estiver disponível, verá uma resposta como esta:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Se o nome já estiver tomado, verá a seguinte resposta:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Crie o seu nome de domínio personalizado

Para ativar um nome de domínio personalizado para o recurso de voz selecionado, utilize o [cmdlet Set-AzCognitiveServicesAccount.](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount)

> [!WARNING]
> Depois de o seguinte código ser executado com sucesso, criará um nome de domínio personalizado para o seu recurso Speech. Lembre-se que este nome *não pode* ser mudado.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Esta secção requer a versão mais recente do Azure CLI. Se estiver a utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que pretende utilizar é gratuito. Utilize o método de disponibilidade de domínio de [verificação](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) a partir da API de Serviços Cognitivos.

Copie o seguinte bloco de código, insira o seu nome de domínio personalizado preferido e guarde para o ficheiro `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copie o ficheiro para a sua pasta atual ou faça o upload para a Azure Cloud Shell e execute o seguinte comando. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pelo seu ID da subscrição do Azure.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Se o nome desejado estiver disponível, verá uma resposta como esta:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Se o nome já estiver tomado, verá a seguinte resposta:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Ativar um nome de domínio personalizado

Para ativar um nome de domínio personalizado para o recurso de voz selecionado, utilize o comando de atualização da [conta az cognitiveservices.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update)

Selecione a subscrição Azure que contém o recurso Speech. Se a sua conta Azure tiver apenas uma subscrição ativa, pode saltar este passo. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pelo seu ID da subscrição do Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Desaprote o nome de domínio personalizado para o recurso selecionado. Substitua os valores dos parâmetros da amostra pelos valores reais e execute o seguinte comando.

> [!WARNING]
> Após a execução bem sucedida do seguinte comando, criará um nome de domínio personalizado para o seu recurso Speech. Lembre-se que este nome *não pode* ser mudado.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Ativar pontos finais privados

Recomendamos a utilização da [zona privada de DNS](../../dns/private-dns-overview.md) anexada à rede virtual com as atualizações necessárias para os pontos finais privados. Cria uma zona de DNS privada por padrão durante o processo de provisionamento. Se estiver a utilizar o seu próprio servidor DNS, também poderá ter de alterar a sua configuração DNS. 

Decida uma estratégia de DNS *antes* de providenciar pontos finais privados para um recurso de discurso de produção. E teste as alterações do DNS, especialmente se utilizar o seu próprio servidor DNS.

Utilize um dos seguintes artigos para criar pontos finais privados. Estes artigos usam uma aplicação web como um recurso de amostra para permitir com pontos finais privados.

- [Criar um ponto final privado utilizando o portal Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado utilizando a Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Criar um ponto final privado utilizando o Azure CLI](../../private-link/create-private-endpoint-cli.md)

Utilize estes parâmetros em vez dos parâmetros do artigo que escolheu:

| Definição             | Valor                                    |
|---------------------|------------------------------------------|
| Tipo de recurso       | **Microsoft.CognitiveServices/contas** |
| Recurso            | **\<your-speech-resource-name>**         |
| Recurso secundário de destino | **da organização**                              |

**DNS para pontos finais privados:** Reveja os princípios gerais do [DNS para os pontos finais privados nos recursos dos Serviços Cognitivos.](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) Em seguida, confirme que a sua configuração DNS está a funcionar corretamente, efetuando as verificações descritas nas seguintes secções.

### <a name="resolve-dns-from-the-virtual-network"></a>Resolver DNS a partir da rede virtual

Este controlo é *necessário.*

Siga estes passos para testar a entrada personalizada de DNS da sua rede virtual:

1. Faça login numa máquina virtual localizada na rede virtual à qual anexou o seu ponto final privado. 
1. Abra uma solicitação de comando do Windows ou uma concha Bash, corra `nslookup` e confirme que resolve com sucesso o nome de domínio personalizado do seu recurso.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Confirme que o endereço IP corresponde ao endereço IP do seu ponto final privado.

### <a name="resolve-dns-from-other-networks"></a>Resolver DNS de outras redes

Efetue esta verificação apenas se tiver ativado a opção **Todas as redes** ou a opção de acesso de Redes **Selecionadas e Pontos Finais Privados** na secção **de Rede** do seu recurso. 

Se planeia aceder ao recurso utilizando apenas um ponto final privado, pode saltar esta secção.

1. Faça login num computador ligado a uma rede que pode aceder ao recurso.
2. Abra uma operação de comando do Windows ou uma shell Bash, corra `nslookup` e confirme que resolve com sucesso o nome de domínio personalizado do seu recurso.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

3. Confirme que o endereço IP corresponde ao endereço IP do seu ponto final privado.

> [!NOTE]
> O endereço IP resolvido aponta para um ponto final de procuração de rede virtual, que envia o tráfego da rede para o ponto final privado para o recurso Serviços Cognitivos. O comportamento será diferente para um recurso com um nome de domínio personalizado, mas *sem* pontos finais privados. Consulte [esta secção](#dns-configuration) para mais detalhes.

## <a name="adjust-existing-applications-and-solutions"></a>Ajustar aplicações e soluções existentes

Um recurso de fala com um domínio personalizado habilitado usa uma forma diferente de interagir com os Serviços de Fala. Isto é verdade para um recurso de fala personalizado, ativado por domínio, com e sem pontos finais privados. A informação nesta secção aplica-se a ambos os cenários.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Use um recurso speech com um nome de domínio personalizado e um ponto final privado habilitado

Um recurso de fala com um nome de domínio personalizado e um ponto final privado habilitado usa uma forma diferente de interagir com os Serviços de Fala. Esta secção explica como utilizar este recurso com os Serviços de Fala REST APIs e o [Speech SDK](speech-sdk.md).

> [!NOTE]
> Um recurso de fala sem pontos finais privados mas com um nome de domínio personalizado habilitado também tem uma forma especial de interagir com os Serviços de Fala. Desta forma, difere do cenário de um recurso de fala com ponta privada. Se tiver esse recurso (por exemplo, tinha um recurso com pontos finais privados mas depois decidiu removê-los), consulte a secção [Utilize um recurso speech com um nome de domínio personalizado e sem pontos finais privados](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Recurso de fala com um nome de domínio personalizado e um ponto final privado: Utilização com as APIs REST

Usaremos como amostra o `my-private-link-speech.cognitiveservices.azure.com` nome DNS (domínio personalizado) para esta secção.

Os Serviços de Fala têm APIs DE REPOUSO para [Discurso-a-Texto](rest-speech-to-text.md) e [Texto-a-Voz](rest-text-to-speech.md). Considere as seguintes informações para o cenário com ponta de lança privada.

O Discurso-a-Texto tem duas APIs de DESCANSO. Cada API serve um propósito diferente, usa diferentes pontos finais, e requer uma abordagem diferente quando você está usando-o no cenário privado-enabled.

As APIs de Repouso Discurso-a-Texto são:
- [A API de repouso de voz para texto v3.0,](rest-speech-to-text.md#speech-to-text-rest-api-v30)que é utilizada para [transcrição de lote](batch-transcription.md) e [discurso personalizado](custom-speech-overview.md). v3.0 é um [sucessor de v2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3)
- API de repouso de voz para texto para áudio curto, que é usado para transcrição [on-line](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 

A utilização da API de Repouso Discurso-a-Texto para áudio curto e a API DE REPOUSO text-to-speech no cenário de ponto final privado é a mesma. É equivalente ao [caso DoDK](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) do Discurso descrito mais tarde neste artigo. 

A API de repouso de voz em texto v3.0 utiliza um conjunto diferente de pontos finais, pelo que requer uma abordagem diferente para o cenário privado habilitado para o ponto final.

As próximas subsecções descrevem ambos os casos.

##### <a name="speech-to-text-rest-api-v30"></a>API de REPOUSO fala-a-texto v3.0

Normalmente, os recursos da fala utilizam [os pontos finais regionais dos Serviços Cognitivos](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para comunicar com a [API de repouso fala-a-texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Estes recursos têm o seguinte formato de nomeação: <p/>`{region}.api.cognitive.microsoft.com`.

Este é um URL de pedido de amostra:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Depois de ativar um domínio personalizado para um recurso de voz (que é necessário para pontos finais privados), esse recurso utilizará o seguinte padrão de nome DNS para o ponto final básico da API REST: <p/>`{your custom name}.cognitiveservices.azure.com`.

Isto significa que, no nosso exemplo, o nome de ponto final da API REST será: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

E o URL do pedido de amostra precisa de ser convertido para:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Este URL deve ser acessível a partir da rede virtual com o ponto final privado anexado (desde que a [resolução DE DNS correta](#resolve-dns-from-the-virtual-network)).

Depois de ativar um nome de domínio personalizado para um recurso de voz, normalmente substitui o nome do anfitrião em todos os URLs solicitados pelo novo nome de anfitrião de domínio personalizado. Todas as outras partes do pedido (como o caminho `/speechtotext/v3.0/transcriptions` no exemplo anterior) permanecem as mesmas.

> [!TIP]
> Alguns clientes desenvolvem aplicações que utilizam a parte da região do nome DNS do ponto final regional (por exemplo, para enviar o pedido para o recurso Discurso implantado na região particular de Azure).
>
> Um domínio personalizado para um recurso de voz não contém *nenhuma* informação sobre a região onde o recurso é implantado. Assim, a lógica de aplicação descrita anteriormente *não* funcionará e precisa de ser alterada.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API de REPOUSO de voz para texto para breve áudio e texto-a-discurso REST API

A [API de Repouso Discurso-a-Texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e a [API DE REPOUSO text-to-speech](rest-text-to-speech.md) utilizam dois tipos de pontos finais:
- [Serviços Cognitivos pontos finais regionais](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para comunicação com os Serviços Cognitivos REST API para obter um token de autorização
- Pontos finais especiais para todas as outras operações

A descrição detalhada dos pontos finais especiais e como o seu URL deve ser transformado para um recurso de fala ativado por pontos privados é fornecida [nesta subsecção](#general-principles) sobre a utilização com o SDK do discurso. O mesmo princípio descrito para o SDK aplica-se à API de repouso fala-a-texto v1.0 e à API de repouso text-to-speech.

Familiarize-se com o material da subsecção mencionada no parágrafo anterior e veja o seguinte exemplo. O exemplo descreve a API DE REPOUSO TEXT-TO-SPEECH. A utilização da API de Repouso Discurso-a-Texto para áudio curto é totalmente equivalente.

> [!NOTE]
> Quando estiver a utilizar a API de Repouso Discurso-a-Texto para áudio curto em cenários de ponto final privado, utilize um token de autorização [passado através](rest-speech-to-text.md#request-headers) do `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar uma chave de subscrição de discurso para o ponto final especial através do `Ocp-Apim-Subscription-Key` cabeçalho *não* funcionará e gerará Error 401.

**Exemplo de utilização da API de REPOUSO text-to-Speech**

Usaremos a Europa Ocidental como uma amostra da região de Azure e `my-private-link-speech.cognitiveservices.azure.com` como uma amostra do nome DNS (domínio personalizado). O nome de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` no nosso exemplo pertence ao recurso Discurso criado na região da Europa Ocidental.

Para obter a lista das vozes apoiadas na região, faça as duas operações seguintes:

- Obter um sinal de autorização:
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- Ao usar o símbolo, obtenha a lista de vozes:
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
Consulte mais detalhes sobre os passos anteriores na documentação da [API de REPOUSO TEXT-to-speech](rest-text-to-speech.md).

Para o recurso de fala ativado por pontos privados, os URLs de ponto final para a mesma sequência de funcionamento devem ser modificados. A mesma sequência será assim:

- Obter um sinal de autorização:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  Consulte a explicação detalhada na [subsecção anterior do API de repouso discurso-a-texto v3.0.](#speech-to-text-rest-api-v30)

- Ao utilizar o token obtido, obtenha a lista de vozes:
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  Consulte uma explicação detalhada na subsecção [de princípios gerais](#general-principles) para o SDK do discurso.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Recurso de fala com um nome de domínio personalizado e um ponto final privado: Utilização com o SDK de fala

A utilização do SDK de discurso com um nome de domínio personalizado e recursos de fala ativados por pontos privados requer que reveja e altere o seu código de aplicação.

Usaremos como amostra o `my-private-link-speech.cognitiveservices.azure.com` nome DNS (domínio personalizado) para esta secção.

##### <a name="general-principles"></a>Princípios gerais

Normalmente em cenários SDK (bem como nos cenários de API DE REPOUSO DE TEXTO PARA FALAR), os recursos da fala utilizam os pontos finais regionais dedicados para diferentes ofertas de serviços. O formato de nome DNS para estes pontos finais é:

`{region}.{speech service offering}.speech.microsoft.com`

Um nome DNS exemplo é:

`westeurope.stt.speech.microsoft.com`

Todos os valores possíveis para a região (primeiro elemento do nome DNS) estão listados nas [regiões apoiadas pelo serviço speech](regions.md). A tabela a seguir apresenta os valores possíveis para a oferta de Serviços de Fala (segundo elemento do nome DNS):

| Valor do nome DNS | Oferta de serviço de fala                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandos Personalizados](custom-commands.md)                       |
| `convai`       | [Transcrição de conversa](conversation-transcription.md) |
| `s2s`          | [Tradução de Voz](speech-translation.md)                 |
| `stt`          | [Discurso-a-Texto](speech-to-text.md)                         |
| `tts`          | [Texto-a-Discurso](text-to-speech.md)                         |
| `voice`        | [Voz Personalizada](how-to-custom-voice.md)                      |

Assim, o exemplo anterior significa `westeurope.stt.speech.microsoft.com` um ponto final de discurso a texto na Europa Ocidental.

Os pontos finais com pontas de lança privados comunicam-se com os Serviços de Fala através de um representante especial. Por isso, tem de *alterar os URLs de ligação* de ponto final. 

Um URL de ponta "standard" parece: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Uma URL de ponta privada parece: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exemplo 1.** Uma aplicação está a comunicar utilizando o seguinte URL (reconhecimento de voz utilizando o modelo base para inglês americano na Europa Ocidental):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Para usá-lo no cenário com ponta privada quando o nome de domínio personalizado do recurso Discurso `my-private-link-speech.cognitiveservices.azure.com` for, deve modificar o URL assim:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Note os detalhes:

- O nome do anfitrião `westeurope.stt.speech.microsoft.com` é substituído pelo nome de anfitrião de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` .
- O segundo elemento do nome DNS original `stt` ( ) torna-se o primeiro elemento do caminho URL e precede o caminho original. Assim, o URL original `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` torna-se `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exemplo 2.** Uma aplicação usa o seguinte URL para sintetizar a fala na Europa Ocidental usando um modelo de voz personalizado:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

O url equivalente a seguir utiliza um ponto final privado ativado, onde o nome de domínio personalizado do recurso Discurso `my-private-link-speech.cognitiveservices.azure.com` é:

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

O mesmo princípio no Exemplo 1 é aplicado, mas o elemento-chave desta vez é `voice` .

##### <a name="modifying-applications"></a>Modificação de aplicações

Siga estes passos para modificar o seu código:

1. Determinar o URL do ponto final da aplicação:

   - [Ative o registo da sua aplicação](how-to-use-logging.md) e execute-o para registar a sua atividade.
   - No ficheiro de registo, procure `SPEECH-ConnectionUrl` . Nas linhas correspondentes, o `value` parâmetro contém o URL completo que a sua aplicação usou para chegar aos Serviços de Fala.

   Exemplo:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Assim, o URL que a aplicação usada neste exemplo é:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Criar um `SpeechConfig` exemplo utilizando um URL de ponto final completo:

   1. Modifique o ponto final que acabou de determinar, como descrito na secção de [princípios gerais](#general-principles) anteriores.

   1. Modifique a forma como cria o exemplo de `SpeechConfig` . Provavelmente, a sua aplicação está a usar algo assim:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Isto não funcionará para um recurso de fala com ponta privada devido às alterações de nome de anfitrião e URL que descrevemos nas secções anteriores. Se tentar executar a sua aplicação existente sem modificações utilizando a chave de um recurso com base no ponto de venda privado, obterá um erro de autenticação (401).

      Para que funcione, modifique a forma como instantaneamente a classe e utilize a `SpeechConfig` inicialização "do ponto final"/"com ponto final". Suponha que temos as seguintes duas variáveis definidas:
      - `subscriptionKey` contém a chave do recurso de fala ativado por pontos privados.
      - `endPoint` contém o URL de ponto final *modificado* completo (utilizando o tipo exigido pela linguagem de programação correspondente). No nosso exemplo, esta variável deve conter:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Criar um `SpeechConfig` exemplo:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Os parâmetros de consulta especificados no ponto final URI não são alterados, mesmo que sejam definidos por outras APIs. Por exemplo, se a linguagem de reconhecimento for definida no URI como parâmetro de `language=en-US` consulta, e também for definida `ru-RU` através da propriedade correspondente, é utilizada a definição de idioma no URI. A linguagem eficaz é `en-US` então.
>
> Os parâmetros definidos no ponto final URI têm sempre precedência. Outras APIs podem sobrepor-se apenas a parâmetros que não são especificados no ponto final URI.

Após esta modificação, a sua aplicação deve funcionar com os recursos de fala ativados por pontos privados. Estamos a trabalhar num apoio mais perfeito a cenários privados de ponto final.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Use um recurso de discurso com um nome de domínio personalizado e sem pontos finais privados

Neste artigo, salientamos várias vezes que permitir um domínio personalizado para um recurso da Fala é *irreversível*. Este recurso utilizará uma forma diferente de comunicar com os Serviços de Fala, em comparação com os que utilizam [nomes de pontos finais regionais.](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)

Esta secção explica como utilizar um recurso de fala com um nome de domínio personalizado habilitado, mas *sem* quaisquer pontos finais privados com os Serviços de Fala REST APIs e [Speech SDK](speech-sdk.md). Este pode ser um recurso que já foi usado num cenário de ponto final privado, mas depois teve os seus pontos finais privados apagados.

#### <a name="dns-configuration"></a>Configuração do DNS

Lembre-se de como um nome DNS de domínio personalizado do recurso de fala ativado por pontos privados é [resolvido a partir de redes públicas](#resolve-dns-from-other-networks). Neste caso, o endereço IP resolvido aponta para um ponto final de procuração para uma rede virtual. Este ponto final é utilizado para o envio do tráfego da rede para o recurso de serviços cognitivos com extremidade privada.

No entanto, quando *todos os* pontos finais privados de recursos são removidos (ou logo após a ativação do nome de domínio personalizado), o registo CNAME do recurso Discurso é reprovisionado. Aponta agora para o endereço IP do ponto final regional dos [Serviços Cognitivos](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)correspondente.

Assim, a saída do `nslookup` comando será assim:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Compare-o com a saída [desta secção.](#resolve-dns-from-other-networks)

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Recurso de fala com um nome de domínio personalizado e sem pontos finais privados: Utilização com as APIs REST

##### <a name="speech-to-text-rest-api-v30"></a>API de REPOUSO fala-a-texto v3.0

A utilização da API de repouso de fala em texto v3.0 é totalmente equivalente ao caso dos recursos de [fala ativados por pontos privados](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API de REPOUSO de voz para texto para breve áudio e texto-a-discurso REST API

Neste caso, a utilização da API de Repouso Discurso-a-Texto para áudio curto e utilização da API REST text-to-speech não tem diferenças em termos gerais, com uma exceção para a API de Repouso Discurso-a-Texto para áudio curto. (Ver a seguinte nota.) Deve utilizar ambas as APIs, conforme descrito na [API REST de voz a texto para](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) documentação curta de API áudio e [texto-a-discurso](rest-text-to-speech.md) REST.

> [!NOTE]
> Quando estiver a utilizar a API de Repouso Discurso-a-Texto para áudio curto em cenários de domínio personalizado, utilize um token de autorização [passado através de](rest-speech-to-text.md#request-headers) um `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar uma chave de subscrição de discurso para o ponto final especial através do `Ocp-Apim-Subscription-Key` cabeçalho *não* funcionará e gerará Error 401.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Recurso de fala com um nome de domínio personalizado e sem pontos finais privados: Utilização com o SDK de fala

A utilização do SDK de discurso com recursos de fala personalizados *sem* pontos finais privados requer a revisão e alterações prováveis do seu código de aplicação. Note que estas alterações são diferentes do caso de um [recurso de fala com ponta privada](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk). Estamos a trabalhar num suporte mais perfeito de cenários privados de ponto final e domínio personalizado.

Usaremos como amostra o `my-private-link-speech.cognitiveservices.azure.com` nome DNS (domínio personalizado) para esta secção.

Na secção sobre [os recursos de fala ativados por pontos privados,](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)explicamos como determinar o URL do ponto final, modificá-lo e fazê-lo funcionar através da inicialização "a partir do ponto final"/"com ponto final" da instância de `SpeechConfig` classe.

No entanto, se tentar executar a mesma aplicação depois de ter todos os pontos finais privados removidos (permitindo algum tempo para a reprovisionamento do registo de DNS correspondente), obterá um erro de serviço interno (404). A razão é que o registo do [DNS](#dns-configuration) agora aponta para o ponto final dos Serviços Cognitivos regionais em vez do proxy de rede virtual, e os caminhos de URL como `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` não serão encontrados lá.

Se reverter a sua aplicação para a instantânea padrão `SpeechConfig` no estilo do seguinte código, a sua aplicação terminará com o erro de autenticação (401):

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>Modificação de aplicações

Para permitir que a sua aplicação utilize um recurso speech com um nome de domínio personalizado e sem pontos finais privados, siga estes passos:

1. Solicite um sinal de autorização da API dos Serviços Cognitivos. [Este artigo](../authentication.md#authenticate-with-an-authentication-token) mostra como obter o símbolo.

   Use o seu nome de domínio personalizado no URL do ponto final. No nosso exemplo, esta URL é:
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > Pode encontrar este URL no portal Azure. Na sua página de recursos de discurso, no grupo **de gestão de recursos,** selecione **Keys e Endpoint**.

1. Crie um `SpeechConfig` exemplo utilizando o sinal de autorização que obteve na secção anterior. Suponha que temos as seguintes variáveis definidas:

   - `token`: o sinal de autorização obtido na secção anterior
   - `azureRegion`: o nome da [região](regions.md) dos recursos da fala (exemplo: `westeurope` )
   - `outError`: (apenas para o [caso do objetivo C)](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror)

   Criar um `SpeechConfig` caso como este:

   ```csharp
   var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
   ```
   ```cpp
   auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
   ```
   ```java
   SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
   ```
   ```python
   import azure.cognitiveservices.speech as speechsdk
   speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
   ```
   ```objectivec
   SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
   ```
> [!NOTE]
> O chamador tem de garantir que o sinal de autorização é válido. Antes que o sinal de autorização expire, o chamador precisa refresca-lo chamando este setter com um novo token válido. Como os valores de configuração são copiados quando se está a criar um novo reconhecimento ou sintetizador, o novo valor simbólico não se aplicará aos reconhecedores ou sintetizadores que já foram criados.
>
> Para estes, desista o sinal de autorização do reconhecedor ou sintetizador correspondente para refrescar o token. Se não refrescar o token, o reconhecedor ou sintetizador encontrará erros durante o funcionamento.

Após esta modificação, a sua aplicação deve funcionar com recursos da Speech que usam um nome de domínio personalizado sem pontos finais privados.

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Saber mais

* [Azure Private Link](../../private-link/private-link-overview.md)
* [API de Voz](speech-sdk.md)
* [API de REPOUSO Discurso-A-Texto](rest-speech-to-text.md)
* [API de REPOUSO Text-to-Speech](rest-text-to-speech.md)
