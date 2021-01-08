---
title: Como usar pontos finais privados com serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar o serviço de fala com pontos finais privados fornecidos pela Azure Private Link
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018535"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Use o serviço de discurso através de um ponto final privado

[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe ligar-se a serviços em Azure utilizando um [ponto final privado.](../../private-link/private-endpoint-overview.md)
Um ponto final privado é um endereço IP privado apenas acessível dentro de uma [rede virtual](../../virtual-network/virtual-networks-overview.md) específica e sub-rede.

Este artigo explica como configurar e usar o Private Link e os pontos finais privados com os Serviços de Fala Cognitiva Azure.

> [!NOTE]
> Este artigo explica as especificidades da configuração e utilização do Link Privado com os Serviços de Fala Cognitiva Azure. Antes de prosseguir, reveja como [utilizar redes virtuais com Serviços Cognitivos.](../cognitive-services-virtual-networks.md)

Execute as seguintes tarefas para utilizar um serviço de fala através de um ponto final privado:

1. [Criar nome de domínio personalizado de recursos de fala](#create-a-custom-domain-name)
2. [Criar e configurar pontos finais privados](#enable-private-endpoints)
3. [Ajustar aplicações e soluções existentes](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Para remover pontos finais privados mais tarde, mas ainda utilizar o recurso Speech, executará as tarefas encontradas [nesta secção](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Crie um nome de domínio personalizado

Os pontos finais privados requerem um [nome de subdomínio personalizado dos Serviços Cognitivos.](../cognitive-services-custom-subdomains.md) Siga as instruções abaixo para criar uma para o seu recurso Discurso.

> [!WARNING]
> Um recurso de fala com nome de domínio personalizado habilitado usa uma forma diferente de interagir com o serviço Desematado.
> Provavelmente deve ajustar o seu código de aplicação para cenários [de fim com ativação e](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) [ **não** de ponto final privado.](#use-speech-resource-with-custom-domain-name-without-private-endpoints)
>
> Quando ativa um nome de domínio personalizado, a operação não é [**reversível**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). A única maneira de voltar ao [nome regional](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) é criar um novo recurso de Discurso.
>
> Se o seu recurso Speech tiver muitos modelos e projetos personalizados associados criados através [do Speech Studio,](https://speech.microsoft.com/) recomendamos **vivamente** experimentar a configuração com um recurso de teste antes de modificar o recurso utilizado na produção.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para criar um nome de domínio personalizado utilizando o portal Azure, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com/) e inscreva-se na sua conta Azure.
1. Selecione o recurso de fala necessário.
1. No grupo **de Gestão de Recursos** no painel de navegação esquerdo, clique em **Rede**.
1. No **separador Firewalls e redes virtuais,** clique em **Gerar Nome de Domínio Personalizado**. Um novo painel direito aparece com instruções para criar um subdomínio personalizado único para o seu recurso.
1. No painel 'Gerar nome de domínio personalizado', insira uma parte de nome de domínio personalizado. Todo o seu domínio personalizado será como: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Depois de criar um nome de domínio personalizado, _não pode_ ser alterado! Relem o alerta acima.** Depois de introduzir o seu nome de domínio personalizado, clique em **Guardar**.
1. Após a conclusão da operação, no grupo **de gestão de recursos,** clique em **Chaves e Ponto Final**. Confirme que o novo nome do ponto final do seu recurso começa desta forma:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um nome de domínio personalizado utilizando o PowerShell, confirme que o seu computador tem a versão 7.x do PowerShell ou posteriormente com a versão 5.1.0 ou posterior do módulo Azure PowerShell. para ver as versões destas ferramentas, siga estes passos:

1. Numa janela PowerShell, escreva:

    `$PSVersionTable`

    Confirme que o valor psversion é superior a 7.x. Para atualizar o PowerShell, siga as instruções na [Instalação de várias versões do PowerShell](/powershell/scripting/install/installing-powershell) para atualizar.

1. Numa janela PowerShell, escreva:

    `Get-Module -ListAvailable Az`

    Se nada aparecer, ou se a versão do módulo Azure PowerShell for inferior a 5.1.0, siga as instruções no [módulo Install Azure PowerShell](/powershell/azure/install-Az-ps) para atualizar.

Antes de prosseguir, corra `Connect-AzAccount` para criar uma ligação com Azure.

## <a name="verify-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que gostaria de utilizar está disponível. Siga estes passos para confirmar que o domínio está disponível usando a operação [de Disponibilidade de Domínio de Verificação](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) na API dos Serviços Cognitivos.

> [!TIP]
> O código abaixo **NÃO** funcionará em Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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

Para ativar o nome de domínio personalizado para o recurso de voz selecionado, utilizamos [o cmdlet Set-AzCognitiveServicesAccount.](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount)

> [!WARNING]
> Depois de o código abaixo ser executado com sucesso, criará um nome de domínio personalizado para o seu recurso Speech.
> Este nome **não pode** ser mudado. Veja mais informações no alerta **de aviso** acima.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
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

- Esta secção requer a versão mais recente do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="verify-the-custom-domain-name-is-available"></a>Verifique se o nome de domínio personalizado está disponível

Verifique se o domínio personalizado que pretende utilizar é gratuito. Utilizaremos o método [de Disponibilidade de Domínio de Verificação](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) da API dos Serviços Cognitivos.

Copie o bloco de código abaixo, insira o nome de domínio personalizado preferido e guarde para o ficheiro `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copie o ficheiro para a sua pasta atual ou faça o upload para a Azure Cloud Shell e execute o seguinte comando. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(Substitua pelo seu ID de assinatura Azure).

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
## <a name="enable-custom-domain-name"></a>Ativar o nome de domínio personalizado

Para ativar o nome de domínio personalizado para o recurso de voz selecionado, utilizamos o comando [de az cognitiveservices de atualização de conta.](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update)

Selecione a subscrição Azure contendo o recurso Speech. Se a sua conta Azure tiver apenas uma subscrição ativa, pode saltar este passo. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(Substitua pelo seu ID de assinatura Azure).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Desaprote o nome de domínio personalizado para o recurso selecionado. Substitua os valores dos parâmetros da amostra pelos valores reais e execute o comando abaixo.

> [!WARNING]
> Após a execução bem sucedida do comando abaixo, criará um nome de domínio personalizado para o seu recurso Speech. Este nome **não pode** ser mudado. Veja mais informações no alerta de precaução acima.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Ativar pontos finais privados

Recomendamos a utilização da [zona privada de DNS](../../dns/private-dns-overview.md) anexada à rede virtual com as atualizações necessárias para os pontos finais privados, que criamos por padrão durante o processo de provisionamento. No entanto, se estiver a utilizar o seu próprio servidor DNS, poderá também necessitar de alterar a sua configuração DE DNS, como mostra _o DNS para pontos finais privados_, abaixo. Decida sobre a estratégia DNS _ *antes** fornecendo pontos finais privados para um recurso de produção Speech, e teste as alterações de DNS, especialmente se utilizar o seu próprio servidor DNS.

Utilize um dos seguintes artigos para criar pontos finais privados. Os artigos usam uma aplicação Web como um recurso de amostra para permitir com pontos finais privados. Utilizará estes parâmetros em vez dos do artigo:

| Definição             | Valor                                    |
|---------------------|------------------------------------------|
| Tipo de recurso       | **Microsoft.CognitiveServices/contas** |
| Recurso            | **\<your-speech-resource-name>**         |
| Recurso secundário de destino | **da organização**                              |

- [Criar um Ponto Final Privado com o portal do Azure](../../private-link/create-private-endpoint-portal.md)
- [Criar um ponto final privado utilizando a Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Criar um ponto final privado usando O Azure CLI](../../private-link/create-private-endpoint-cli.md)

**DNS para pontos finais privados:** Reveja os princípios gerais do [DNS para os pontos finais privados nos recursos dos Serviços Cognitivos.](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) Em seguida, confirme que a sua configuração DE DNS está a funcionar corretamente, realizando estas verificações:

### <a name="resolve-dns-from-the-virtual-network"></a>Resolver DNS a partir da rede virtual

Este controlo é **necessário.**

Siga estes passos para testar a entrada personalizada de DNS da sua rede virtual.

1. Faça login numa máquina virtual localizada na rede virtual à qual anexou o seu ponto final privado. 
1. Abra o Comando do Windows Prompt ou a shell Bash, corra `nslookup` e confirme que resolve com sucesso o nome de domínio personalizado do seu recurso.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Confirme que o endereço IP corresponde ao endereço IP do seu ponto final privado.

### <a name="resolve-dns-from-other-networks"></a>Resolver DNS de outras redes

Só efetue esta verificação se pretender utilizar o seu recurso de fala ativado por cabo privado no modo "híbrido", onde ativou **todas as redes** ou redes selecionadas e a opção de acesso de pontos de final **privados** na secção **de Rede** do seu recurso. Se planeia aceder ao recurso utilizando apenas um ponto final privado, pode saltar esta secção.

1. Faça login num computador ligado a uma rede autorizada a aceder ao recurso.
2. Abra o Comando do Windows Prompt ou a shell Bash, corra `nslookup` e confirme que resolve com sucesso o nome de domínio personalizado do seu recurso.

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

Um recurso de fala com um domínio personalizado habilitado usa uma forma diferente de interagir com os Serviços de Fala. Isto é verdade para um domínio personalizado habilitado recurso de fala com e sem pontos finais privados. A informação nesta secção aplica-se a ambos os cenários.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Use recurso de discurso com nome de domínio personalizado e ponto final privado ativado

Um recurso de fala com nome de domínio personalizado e ponto final privado habilitado usa uma forma diferente de interagir com os Serviços de Fala. Esta secção explica como utilizar este recurso com serviços de fala REST API e [Speech SDK](speech-sdk.md).

> [!NOTE]
> Por favor, note que um Recurso de Fala sem pontos finais privados, mas com **o nome de domínio personalizado** habilitado também tem uma forma especial de interagir com os Serviços de Fala, mas desta forma difere do cenário de um recurso de fala ativado por um ponto final privado. Se tiver esse recurso (digamos, tinha um recurso com pontos finais privados, mas depois decidiu removê-los) certifique-se de que se familiariza com a [secção correspondente](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Recurso de fala com nome de domínio personalizado e ponto final privado. Utilização com REST API

Utilizaremos `my-private-link-speech.cognitiveservices.azure.com` como amostra o nome DNS (domínio personalizado) para esta secção.

##### <a name="note-on-speech-services-rest-api"></a>Nota sobre serviços de fala REST API

Os Serviços de Fala têm API REST para [Discurso-a-Texto](rest-speech-to-text.md) e [Texto-a-voz](rest-text-to-speech.md). O seguinte deve ser considerado para o cenário privado habilitado para o ponto final.

Falar-a-texto tem duas APIs de DESCANSO diferentes. Cada API serve um propósito diferente, usa diferentes pontos finais, e requer uma abordagem diferente quando usado canta em cenário privado habilitado para o ponto final.

As APIs de repouso de discurso para texto são:
- [O API REST de voz em texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) é utilizado para [a transcrição do lote](batch-transcription.md) e discurso [personalizado](custom-speech-overview.md). v3.0 é um [sucessor de v2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [A API REST de voz para texto para áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) é usada para a transcrição onLine. 

O uso da API rest de voz para texto para a API de áudio curto e texto-a-discurso REST no cenário de ponto final privado é o mesmo e equivalente ao [caso SDK de discurso](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) descrito mais tarde neste artigo. 

A API REST v3.0 de voz para texto está a utilizar um conjunto diferente de pontos finais e, portanto, requer uma abordagem diferente para o cenário privado ativado pelo ponto final.

Ambos os casos são descritos nas próximas subsecções.


##### <a name="speech-to-text-rest-api-v30"></a>A API DE REPOUSO de expressão em texto v3.0

Normalmente, os recursos da fala utilizam [os pontos finais regionais dos Serviços Cognitivos](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para comunicar com a [API rest de voz para texto v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Estes recursos têm o seguinte formato de nomeação: <p/>`{region}.api.cognitive.microsoft.com`

Este é um URL de pedido de amostra:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Depois de permitir o domínio personalizado para um recurso de voz (que é necessário para pontos finais privados), esse recurso utilizará o seguinte padrão de nome DNS para o ponto final básico da API REST: <p/>`{your custom name}.cognitiveservices.azure.com`

Isto significa que, no nosso exemplo, o nome de ponto final da API REST será: <p/>`my-private-link-speech.cognitiveservices.azure.com`

E o URL de pedido de amostra supria que se convertesse para:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Este URL deve ser acessível a partir da rede virtual com o ponto final privado anexado (desde que a [resolução DE DNS correta](#resolve-dns-from-the virtual-network)).

Normalmente, depois de permitir o nome de domínio personalizado para um recurso de voz, substituirá o nome de anfitrião em todos os URLs solicitados pelo novo nome de anfitrião de domínio personalizado. Todas as outras partes do pedido (como o caminho `/speechtotext/v3.0/transcriptions` no exemplo acima) permanecem as mesmas.

> [!TIP]
> Alguns clientes desenvolveram aplicações que utilizam a parte da região do nome DNS do ponto final regional (por exemplo, para enviar o pedido para o recurso Discurso implantado na região particular de Azure).
>
> O nome de domínio personalizado de recursos de fala não contém **nenhuma** informação sobre a região onde o recurso é implantado. Assim, a lógica de aplicação descrita acima **não** funcionará e precisa de ser alterada.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de voz para texto para breve áudio e API DE REPOUSO TEXT-to-speech

[A API REST de voz para texto para a](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) [API](rest-text-to-speech.md) de áudio curto e de texto para voz REST utiliza dois tipos de pontos finais:
- [Serviços Cognitivos pontos finais regionais](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) para comunicação com os Serviços Cognitivos REST API para obter um token de autorização
- Pontos finais especiais para todas as outras operações

A descrição detalhada dos pontos finais especiais e como o seu URL deve ser transformado para um recurso de fala ativado por telefone privado é fornecida [nesta subsecção](#general-principle) da secção "Utilização com SDK de fala" abaixo. O mesmo princípio descrito para SDK aplica-se à API de Discurso-a-Texto e DE SMS-a-falar.

Familiarize-se com o material da subsecção mencionada no parágrafo anterior e veja o seguinte exemplo. (O exemplo descreve a API DE REPOUSO text-to-speech; a utilização da API REST de voz para texto para áudio curto é totalmente equivalente)

> [!NOTE]
> Ao utilizar **a API REST de voz para texto para áudio curto** em cenários de ponto final privado, utilize um token de autorização passado através [do](rest-speech-to-text.md#request-headers) `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar a chave de subscrição de discurso para o ponto final especial através do `Ocp-Apim-Subscription-Key` cabeçalho **não** funcionará e gerará Error 401.

**Exemplo de utilização da API de REPOUSO TEXT-to-speech.**

Usaremos a Europa Ocidental como uma amostra da Região Azure e `my-private-link-speech.cognitiveservices.azure.com` como uma amostra do nome DNS (domínio personalizado). O nome de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` no nosso exemplo pertence ao recurso Discurso criado na região da Europa Ocidental.

Para obter a lista das vozes apoiadas na região é necessário fazer as duas operações seguintes:

- Obter ficha de autorização:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Usando o símbolo, obtenha a lista de vozes:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Ver mais detalhes sobre os passos acima na [documentação da API do REPOUSO text-to-speech](rest-text-to-speech.md))

Para o ponto final privado ativado O recurso de fala, os URLs de ponto final para a mesma sequência de funcionamento precisam de ser modificados. A mesma sequência será assim:
- Obter sinal de autorização via
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(ver explicação pormenorizada na [subsecção API de apoio à fala-a-texto v3.0](#speech-to-text-rest-api-v30) acima)
- Usando o símbolo obtido obter a lista de vozes através
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(ver explicação pormenorizada na subsecção geral do [princípio](#general-principle) "Utilização com SDK de fala" abaixo)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Recurso de fala com nome de domínio personalizado e ponto final privado. Uso com Discurso SDK

A utilização de SDK de discurso com nome de domínio personalizado e recursos de endereços privados ativados requer a revisão e alterações prováveis do seu código de aplicação.

Utilizaremos `my-private-link-speech.cognitiveservices.azure.com` como amostra o nome DNS (domínio personalizado) para esta secção.

##### <a name="general-principle"></a>Princípio geral

Normalmente em cenários SDK (bem como nos cenários de API DE REPOUSO Text-to-speech) Os recursos de voz utilizam os pontos finais regionais dedicados para diferentes ofertas de serviços. O formato de nome DNS para estes pontos finais é: </p>`{region}.{speech service offering}.speech.microsoft.com`

Exemplo: </p>`westeurope.stt.speech.microsoft.com`

Todos os valores possíveis para a região (primeiro elemento do nome DNS) estão [listados aqui](regions.md) Este quadro abaixo apresenta o valor possível para a oferta de Serviços de Fala (segundo elemento do nome DNS):

| Valor do nome DNS | Oferta de Serviços de Fala                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandos Personalizados](custom-commands.md)                       |
| `convai`       | [Transcrição de conversa](conversation-transcription.md) |
| `s2s`          | [Tradução de Voz](speech-translation.md)                 |
| `stt`          | [Conversão de voz em texto](speech-to-text.md)                         |
| `tts`          | [Conversão de texto em voz](text-to-speech.md)                         |
| `voice`        | [Voz Personalizada](how-to-custom-voice.md)                      |

Assim, o exemplo acima `westeurope.stt.speech.microsoft.com` significa ponto final discurso-a-texto na Europa Ocidental.

Os pontos finais privados habilitados para os pontos finais comunicam com os Serviços de Fala através de um representante especial e, por isso, tem de **alterar os URLs** de ligação de ponto final . 

Um URL de ponta "standard" parece: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Uma URL de ponta privada parece: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exemplo 1.** A aplicação está a comunicar utilizando o seguinte URL (reconhecimento de voz utilizando um modelo base para inglês americano na Europa Ocidental):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Para usá-lo no cenário de ponto final privado ativado quando o nome de domínio personalizado do recurso Discurso `my-private-link-speech.cognitiveservices.azure.com` é que deve modificar o URL assim:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Note os detalhes:

- O nome de anfitrião é substituído pelo nome `westeurope.stt.speech.microsoft.com` de anfitrião de domínio personalizado `my-private-link-speech.cognitiveservices.azure.com` .
- Segundo elemento do nome DNS original ( `stt` ) torna-se o primeiro elemento do caminho URL e precede o caminho original. Assim, o URL original `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` torna-se `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Exemplo 2.** A aplicação utiliza o seguinte URL para sintetizar a fala na Europa Ocidental usando um modelo de voz personalizado):
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Segue-se um URL equivalente que utiliza um ponto final privado ativado onde o nome de domínio personalizado do recurso Discurso `my-private-link-speech.cognitiveservices.azure.com` é:

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

O mesmo princípio do Exemplo 1 é aplicado, mas o elemento-chave desta vez é `voice` .

##### <a name="modify-applications"></a>Modificar aplicações

Siga estes passos para modificar o seu código:

**1. Determinar URL de ponto final de aplicação**

- [Ative o registo da sua aplicação](how-to-use-logging.md) e execute-o para registar a sua atividade.
- No ficheiro de registo, procure `SPEECH-ConnectionUrl` . Nas linhas correspondentes, o `value` parâmetro contém o URL completo que a sua aplicação usou para chegar ao serviço Desemaçamento.

Exemplo:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

Assim, o URL utilizado pela aplicação neste exemplo é:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. Criar `SpeechConfig` instância utilizando URL de ponto final completo**

Modifique o ponto final que determinou na secção anterior, conforme descrito no [princípio geral](#general-principle) acima.

Agora modifique como cria o exemplo de `SpeechConfig` . Provavelmente a sua aplicação de hoje está a usar algo assim:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Isto não funcionará para o recurso de fala ativado por parte do ponto de sessão privado devido às alterações de nome de hospedeiro e URL que descrevemos nas secções anteriores. Se tentar executar a sua aplicação existente sem modificações utilizando a Chave de um recurso privado ativado por um ponto final, obterá erro de autenticação (401).

Para que funcione, modifique a forma como instantaneamente `SpeechConfig` a classe e utilize a inicialização "do ponto final" / "com ponto final". Suponha que temos as seguintes duas variáveis definidas:
- `subscriptionKey` contendo a chave do ponto final privado habilitado recurso de fala
- `endPoint` contendo o URL de ponto final **modificado** completo (utilizando o tipo exigido pela linguagem de programação correspondente). No nosso exemplo esta variável deve conter
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Em seguida, criar um `SpeechConfig` exemplo:
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
> Os parâmetros de consulta especificados no ponto final URI não são alterados, mesmo que sejam definidos por quaisquer outras APIs. Por exemplo, se a linguagem de reconhecimento for definida no URI como parâmetro de consulta "language=en-US", e também é definida como "ru-RU" através da propriedade correspondente, a definição de idioma no URI é usada, e a língua eficaz é "en-US". Os parâmetros definidos no ponto final URI têm sempre precidência. Apenas os parâmetros que não são especificados no ponto final URI podem ser ultrapassados por outras APIs.

Após esta modificação, a sua aplicação deverá funcionar com os recursos de fala ativados por si. Estamos a trabalhar num apoio mais perfeito ao cenário de ponto final privado.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Use recurso de discurso com nome de domínio personalizado sem pontos finais privados

Neste artigo, salientámos várias vezes que permitir o domínio personalizado para um recurso de Voz é **irreversível** e esse recurso utilizará uma forma diferente de comunicar com os serviços de Fala em comparação com os "habituais" (que são os que utilizam [nomes de ponto final regionais).](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)

Esta secção explica como usar um recurso de voz com um nome de domínio personalizado habilitado, mas **sem** quaisquer pontos finais privados com serviços de fala REST API e [Speech SDK](speech-sdk.md). Este pode ser um recurso que já foi usado num cenário de ponto final privado, mas que depois teve o seu ponto final privado eliminado.

#### <a name="dns-configuration"></a>Configuração do DNS

Lembre-se de como um nome DNS de domínio personalizado do ponto final privado habilitado o recurso de fala é [resolvido a partir de redes públicas](#resolve-dns-from-other-networks). Neste caso, o endereço IP resolvido aponta para um ponto final VNet Proxy, que é utilizado para despachar o tráfego da rede para o ponto final privado habilitado o recurso de Serviços Cognitivos.

No entanto, quando **todos os** pontos finais privados de recursos são removidos (ou logo após a habilitação do nome de domínio personalizado) o registo CNAME do recurso Discurso é reprovisionado e aponta agora para o endereço IP do ponto final regional correspondente [dos Serviços Cognitivos](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Recurso de fala com nome de domínio personalizado sem pontos finais privados. Utilização com REST API

##### <a name="speech-to-text-rest-api-v30"></a>A API DE REPOUSO de expressão em texto v3.0

A utilização do API de fala em texto v3.0 é totalmente equivalente ao caso dos [recursos de fala ativados por ponto final privado](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de voz para texto para breve áudio e API DE REPOUSO TEXT-to-speech

Neste caso, a API REST de voz a texto para curto áudio e utilização da API de REPOUSO text-to-speech não tem diferenças no caso geral, com uma exceção para a API rest de voz a texto para áudio curto (ver Nota abaixo). Ambas as APIs devem ser utilizadas como descrito na [API REST de voz a texto para](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) documentação [API de](rest-text-to-speech.md) áudio curto e de voz REST.

> [!NOTE]
> Ao utilizar **a API REST de voz para texto para áudio curto** em cenários de domínio personalizados, utilize um token de autorização passado através [do](rest-speech-to-text.md#request-headers) `Authorization` [cabeçalho](rest-speech-to-text.md#request-headers). Passar a chave de subscrição de discurso para o ponto final especial através do `Ocp-Apim-Subscription-Key` cabeçalho **não** funcionará e gerará Error 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Recurso de fala com nome de domínio personalizado sem pontos finais privados. Uso com Discurso SDK

A utilização de SDK de discurso com nome de domínio personalizado habilitado Recursos de fala **sem** pontos finais privados requer a revisão e alterações prováveis do seu código de aplicação. Note que estas alterações são **diferentes** em comparação com o caso de um [recurso de fala ativado por um ponto final privado](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Estamos trabalhando em suporte mais perfeito do cenário de domínio privado/personalizado.

Utilizaremos `my-private-link-speech.cognitiveservices.azure.com` como amostra o nome DNS (domínio personalizado) para esta secção.

Na secção sobre o [ponto final privado habilitado o recurso de fala](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) explicamos como determinar o URL de ponto final utilizado, modificá-lo e fazê-lo funcionar através da inicialização "do ponto final"/"com ponto final" da instância de `SpeechConfig` classe.

No entanto, se tentar executar a mesma aplicação depois de ter todos os pontos finais privados removidos (permitindo algum tempo à reprovisionamento de registos de DNS correspondentes) obterá erro de serviço interno (404). A razão é o [registo do DNS](#dns-configuration) que agora aponta para o ponto final dos Serviços Cognitivos regionais em vez do proxy VNet, e os caminhos url como `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` não serão encontrados lá, daí o erro "Não encontrado" (404).

Se "reverter" a sua aplicação para a instantiação "padrão" de `SpeechConfig` no estilo de
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
a sua aplicação terminará com o erro de autenticação (401).

##### <a name="modifying-applications"></a>Modificação de aplicações

Para permitir que a sua aplicação utilize um recurso speech com um nome de domínio personalizado e sem pontos finais privados, siga estes passos:

**1. Pedido de Autorização Token dos Serviços Cognitivos REST API**

[Este artigo](../authentication.md#authenticate-with-an-authentication-token) mostra como obter o símbolo usando a API dos Serviços Cognitivos.

Use o seu nome de domínio personalizado no URL do ponto final, que está no nosso exemplo este URL é:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Pode encontrar este URL no portal Azure. Na sua página de recursos de discurso, no âmbito do grupo **de gestão de recursos,** selecione **Keys e Endpoint**.

**2. Criar um `SpeechConfig` exemplo utilizando o método "a partir de um token de autorização" / "com ficha de autorização".**

Crie um `SpeechConfig` caso utilizando o sinal de autorização que obteve na secção anterior. Suponha que temos as seguintes variáveis definidas:

- `token`: o sinal de autorização obtido na secção anterior
- `azureRegion`: o nome da [região](regions.md) dos recursos da fala (exemplo: `westeurope` )
- `outError`: (apenas para [o caso C)](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror)

Em seguida, criar um `SpeechConfig` exemplo:

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
> O chamador tem de garantir que o sinal de autorização é válido.
> Antes que o sinal de autorização expire, o chamador precisa refresca-lo chamando este setter com um novo token válido.
> À medida que os valores de configuração são copiados ao criar um novo reconhecimento ou sintetizador, o novo valor simbólico não se aplicará aos reconhecedores ou sintetizadores que já tenham sido criados.
> Para estes, desista o sinal de autorização do reconhecedor ou sintetizador correspondente para refrescar o token.
> Se não refrescar o token, o reconhecedor ou sintetizador encontrará erros durante o funcionamento.

Após esta modificação, a sua aplicação deve funcionar com recursos da Speech que utilizam um nome de domínio personalizado sem pontos finais privados.

## <a name="pricing"></a>Preços

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Saiba mais

* [Azure Private Link](../../private-link/private-link-overview.md)
* [API de Voz](speech-sdk.md)
* [API REST de conversão de voz em texto](rest-speech-to-text.md)
* [API REST de conversão de texto em voz](rest-text-to-speech.md)
