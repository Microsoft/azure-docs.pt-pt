---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais na aka.ms/skyeye/meta.
título: Streaming Endpoints (Origem) : Descrição do Azure Media Services: Saiba mais sobre o streaming Endpoints (Origin), um serviço dinâmico de embalagem e streaming que entrega conteúdo diretamente a uma aplicação do leitor cliente ou a uma Rede de Entrega de Conteúdos (CDN). serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 02/13/2020 ms.author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming Endpoints (Origem) em Azure Media Services

No Microsoft Azure Media Services, um [Serviço de Streaming Endpoint](/rest/api/media/streamingendpoints) representa um serviço dinâmico (just-in-time) de embalagem e origem que pode entregar o seu conteúdo ao vivo e a pedido diretamente a uma aplicação de cliente usando um dos protocolos comuns de streaming de meios de comunicação (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece encriptação dinâmica (just-in-time) para DRMs líderes do setor. 

Quando cria uma conta de Serviços de Mídia, é criado um Ponto Final de Streaming **predefinido** para si num estado parado. Mais pontos finais de streaming podem ser criados na conta (ver [Quotas e limites).](limits-quotas-constraints-reference.md)

> [!NOTE]
> Para começar a transmitir vídeos, tem de iniciar o **Streaming Endpoint** a partir do qual pretende transmitir o vídeo.
>
> Só és cobrado quando o teu Streaming Endpoint está no estado de execução.

Certifique-se de rever também o tópico [de embalagem Dynamic.](encode-dynamic-packaging-concept.md) 

## <a name="naming-convention"></a>Convenção de nomeação

O formato do nome do anfitrião do URL de streaming é: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , onde = o nome do ponto final de streaming ou o nome do evento ao `servicename` vivo.

Ao utilizar o ponto final de streaming `servicename` predefinido, é omitido de modo a que o URL seja: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Limitações

* O nome do ponto final de streaming tem um valor máximo de 24 caracteres.
* O nome deve seguir este padrão [regex:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Tipos

Existem dois tipos **de Ponto final de streaming:** **Standard** (pré-visualização) e **Premium**. O tipo é definido pelo número de unidades de escala ( `scaleUnits` ) que atribui para o ponto final de streaming.

O limite máximo da unidade de streaming é geralmente de 10. Contacte-nos [aqui](https://azure.microsoft.com/support/create-ticket/) para elevar o limite da sua conta.

A tabela descreve os tipos:

|Tipo|Unidades de escala|Description|
|--------|--------|--------|  
|**Standard**|0|O ponto final de streaming predefinido é um tipo **standard** — pode ser alterado para o tipo Premium `scaleUnits` ajustando-o .|
|**Premium**|>0|**Premium** Os pontos finais de streaming são adequados para cargas de trabalho avançadas e fornecem capacidade de largura de banda dedicada e escalável. Move-se para um tipo **Premium** ajustando `scaleUnits` (unidades de streaming). `scaleUnits` fornecer-lhe uma capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Ao utilizar o tipo **Premium,** cada unidade ativada fornece uma capacidade de largura de banda adicional à aplicação. |

> [!NOTE]
> Para clientes que procuram entregar conteúdo a grandes audiências de internet, recomendamos que você ative a CDN no Streaming Endpoint.

Para obter informações sobre SLA, consulte [preços e SLA.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="comparing-streaming-types"></a>Comparar tipos de streaming

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma produção eficaz muito maior quando um CDN é usado.|200 Mbps por unidade de streaming (SU). Pode fornecer uma produção eficaz muito maior quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A faturação é prostimada| Diário|Diário
Encriptação dinâmica|Yes|Yes
Empacotamento dinâmico|Yes|Yes
Escala|A escala automática sobe até à produção visada.|SUs adicionais
Filtragem IP/G20/Anfitrião personalizado <sup>1</sup>|Yes|Yes
Download progressivo|Yes|Yes
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.

<sup>1</sup> Apenas utilizado diretamente no Ponto de Final de Streaming quando o CDN não estiver ativado no ponto final.<br/>

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|Unidades de escala|CDN|Faturação|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássico|1.0|0|ND|Gratuito|
|Ponto final de streaming padrão (pré-visualização)|2.0|0|Yes|Paga|
|Unidades de streaming premium|1.0|>0|Yes|Paga|
|Unidades de streaming premium|2.0|>0|Yes|Paga|

### <a name="features"></a>Funcionalidades

Funcionalidade|Standard|Premium
---|---|---
Débito |Até 600 Mbps e pode fornecer uma produção eficaz muito maior quando um CDN é usado.|200 Mbps por unidade de streaming (SU). Pode fornecer uma produção eficaz muito maior quando um CDN é usado.
CDN|Azure CDN, CDN de terceiros, ou sem CDN.|Azure CDN, CDN de terceiros, ou sem CDN.
A faturação é prostimada| Diário|Diário
Encriptação dinâmica|Yes|Yes
Empacotamento dinâmico|Yes|Yes
Escala|A escala automática sobe até à produção visada.|Unidades de streaming adicionais.
Filtragem IP/G20/Anfitrião personalizado <sup>1</sup>|Yes|Yes
Download progressivo|Yes|Yes
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> Apenas utilizado diretamente no ponto de ponta de streaming quando o CDN não estiver ativado no ponto final.<br/>

Para obter informações sobre SLA, consulte [preços e SLA.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Ação
---|---|---
Clássica|Standard|Necessidade de optar
Clássico|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássico|Não disponível (Se a versão de ponto final de streaming for 1.0. É permitido mudar para clássico com escalas de definição para "0")
Padrão (com/sem CDN)|Premium com as mesmas configurações|Permitido no estado **iniciado.** (via portal Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (via portal Azure)
Padrão (com/sem CDN)|Premium com config diferente|Permitido no estado **parado** (via portal Azure). Não é permitido no estado de corrida.
Premium (com/sem CDN)|Padrão com config diferente|Permitido no estado **parado** (via portal Azure). Não é permitido no estado de corrida.
Versão 1.0 com SU >= 1 com CDN|Standard/Premium sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.**
Versão 1.0 com SU >= 1 com CDN|Padrão com/sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.** A versão 1.0 CDN será eliminada e nova criada e iniciada.
Versão 1.0 com SU >= 1 com CDN|Premium com/sem CDN|Permitido no estado **parado.** Não é permitido no estado **iniciado.** O CDN clássico será eliminado e o novo criado e iniciado.







## <a name="streaming-endpoint-properties"></a>Propriedades de Streaming Endpoint

Esta secção dá detalhes sobre algumas das propriedades do Streaming Endpoint. Por exemplo, como criar um novo ponto final de streaming e descrições de todas as propriedades, consulte [streaming Endpoint](/rest/api/media/streamingendpoints/create).

- `accessControl`: Utilizado para configurar as seguintes definições de segurança para este ponto final de streaming: teclas de autenticação do cabeçalho de assinatura da Akamai e endereços IP que podem ser ligados a este ponto final. Esta propriedade só pode ser definida quando `cdnEnabled` é definida como falsa.

- `cdnEnabled`: Indica se a integração do Azure CDN para este ponto final de streaming está ativada (desativada por defeito). Se definir `cdnEnabled` como verdadeiro, as seguintes configurações ficam desativadas: `customHostNames` e `accessControl` .

    Nem todos os centros de dados suportam a integração do Azure CDN. Para verificar se o seu centro de dados tem a integração Azure CDN disponível, faça os seguintes passos:

  - Tente definir o `cdnEnabled` verdadeiro.
  - Verifique o resultado devolvido de um `HTTP Error Code 412` (Pré-ConditionFailed) com uma mensagem de "Streaming endpoint CdnEnabled propriedade não pode ser definido como verdadeiro, uma vez que a capacidade de CDN não está disponível na região atual."

    Se tiver este erro, o centro de dados não o suporta. Tente outro centro de dados.

- `cdnProfile`: Quando `cdnEnabled` é definido como verdadeiro, também pode passar `cdnProfile` valores. `cdnProfile` é o nome do perfil CDN onde o ponto final do CDN será criado. Pode fornecer um cdnProfile existente ou utilizar um novo. Se o valor for NULO e `cdnEnabled` for verdadeiro, é utilizado o valor predefinido "AzureMediaStreamingPlatformCdnProfile". Se o fornecido `cdnProfile` já existe, um ponto final é criado sob ele. Se o perfil não existir, um novo perfil é automaticamente criado.
- `cdnProvider`: Quando o CDN está ativado, também pode passar `cdnProvider` valores. `cdnProvider` controlos que o fornecedor será utilizado. Atualmente, três valores são suportados: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for fornecido e `cdnEnabled` for verdadeiro, "StandardVerizon" é usado (é esse o valor padrão).
- `crossSiteAccessPolicies`: Usado para especificar políticas de acesso a vários clientes. Para obter mais informações, consulte [a especificação do ficheiro de política de domínio transversal](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e a [disponibilização de um serviço através dos limites do domínio](/previous-versions/azure/azure-services/gg185950(v=azure.100)). As definições aplicam-se apenas ao Streaming Suave.
- `customHostNames`: Usado para configurar um Streaming Endpoint para aceitar o tráfego direcionado para um nome de anfitrião personalizado. Esta propriedade é válida para Pontos Finais Standard e Premium Streaming e pode ser definida quando `cdnEnabled` : falso.

    A propriedade do nome de domínio deve ser confirmada pela Media Services. Os Serviços de Comunicação Social verificam a propriedade do nome de domínio exigindo um `CName` registo que contenha o ID da conta dos Serviços de Mídia como componente a ser adicionado ao domínio em uso. Como exemplo, para que "sports.contoso.com" seja usado como um nome de anfitrião personalizado para o ponto final de streaming, um registo `<accountId>.contoso.com` para deve ser configurado para apontar para um dos nomes dos anfitriões de verificação de Serviços de Comunicação. O nome do anfitrião de verificação é composto por verificações. `\<mediaservices-dns-zone>` . .

    Seguem-se as zonas de DNS previstas para serem utilizadas no registo de verificação de diferentes regiões de Azure.
  
  - América do Norte, Europa, Singapura, Hong Kong SAR, Japão:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Por exemplo, um `CName` registo que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" comprova que o ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem a propriedade do domínio contoso.com, permitindo assim que qualquer nome sob contoso.com seja usado como nome de anfitrião personalizado para um ponto de streaming sob essa conta. Para encontrar o valor de ID do Serviço de Mídia, vá ao [portal Azure](https://portal.azure.com/) e selecione a sua conta de Media Service. O **ID da Conta** aparece no topo direito da página.

    Se houver uma tentativa de definir um nome de anfitrião personalizado sem uma verificação adequada do `CName` registo, a resposta do DNS falhará e ficará em cache durante algum tempo. Uma vez que um registo adequado esteja no lugar, pode demorar um pouco até que a resposta em cache seja revalidada. Dependendo do fornecedor DNS para o domínio personalizado, leva de alguns minutos a uma hora para revalidar o registo.

    Além dos `CName` mapas `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>` para, deve criar outro `CName` que mapeie o nome de anfitrião personalizado (por exemplo, `sports.contoso.com` ) para o nome de anfitrião do seu Serviço de Media Streaming Endpoint (por exemplo, `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Streaming Endpoints localizados no mesmo centro de dados não podem partilhar o mesmo nome de anfitrião personalizado.

    Atualmente, os Media Services não suportam TLS com domínios personalizados.

- `maxCacheAge` - Substitui o cabeçalho de controlo de cache HTTP de idade máxima padrão definido pelo ponto final de streaming em fragmentos de meios de comunicação e manifestos a pedido. O valor é definido em segundos.
- `resourceState` -

    - Parado: o estado inicial de um ponto final de streaming após a criação
    - Início: está em transição para o estado de execução
    - Execução: é capaz de transmitir conteúdo para os clientes
    - Dimensionamento: as unidades de escala estão a ser aumentadas ou diminuídas
    - Paragem: está em transição para o estado parado
    - Eliminação: está a ser suprimida

- `scaleUnits`: Forneça-lhe uma capacidade de saída dedicada que pode ser adquirida em incrementos de 200 Mbps. Se precisar de passar para um tipo **Premium,** `scaleUnits` ajuste.

## <a name="why-use-multiple-streaming-endpoints"></a>Porquê utilizar vários pontos finais de streaming?

Um único ponto final de streaming pode transmitir vídeos ao vivo e a pedido e a maioria dos clientes usa apenas um ponto final de streaming. Esta secção dá alguns exemplos do porquê de ter de utilizar vários pontos finais de streaming.

* Cada unidade reservada permite 200 Mbps de largura de banda. Se precisar de mais de 2.000 Mbps (2 Gbps) de largura de banda, pode utilizar o segundo ponto final de streaming e o equilíbrio de carga para lhe dar largura de banda adicional.

    No entanto, a CDN é a melhor forma de obter escala para o streaming de conteúdos, mas se estiver a fornecer tanto conteúdo que o CDN está a puxar mais de 2 Gbps, então pode adicionar pontos finais de streaming adicionais (origens). Neste caso, você precisaria de distribuir URLs de conteúdo que são equilibrados em todos os dois pontos finais de streaming. Esta abordagem proporciona melhor caching do que tentar enviar pedidos para cada origem aleatoriamente (por exemplo, através de um gestor de tráfego). 
    
    > [!TIP]
    > Normalmente, se o CDN estiver a puxar mais de 2 Gbps, então algo pode estar mal configurado (por exemplo, sem escudo de origem).
    
* Carga equilibrando diferentes fornecedores de CDN. Por exemplo, pode configurar o ponto final de streaming padrão para usar o CDN Verizon e criar um segundo para usar o Akamai. Em seguida, adicione um pouco de equilíbrio de carga entre os dois para obter o equilíbrio multi-CDN. 

    No entanto, muitas vezes o cliente faz o equilíbrio de carga em vários fornecedores de CDN usando uma única origem.
* Streaming de conteúdo misto: Live and Video on Demand. 

    Os padrões de acesso para conteúdos ao vivo e a pedido são muito diferentes. O conteúdo ao vivo tende a receber muita procura pelo mesmo conteúdo de uma só vez. O conteúdo em vídeo a pedido (conteúdo de arquivo de cauda longa, por exemplo) tem uma utilização baixa no mesmo conteúdo. Assim, o caching funciona muito bem no conteúdo ao vivo, mas não tão bem no conteúdo da cauda longa.

    Considere um cenário em que os seus clientes estão principalmente a ver conteúdos ao vivo, mas só ocasionalmente estão a ver conteúdos a pedido e é servido a partir do mesmo Streaming Endpoint. O baixo uso do conteúdo a pedido ocuparia espaço de cache que seria melhor guardado para o conteúdo ao vivo. Neste cenário, recomendamos servir o conteúdo ao vivo de um Streaming Endpoint e o conteúdo de cauda longa de outro Streaming Endpoint. Isto melhorará o desempenho do conteúdo do evento ao vivo.
    
## <a name="scaling-streaming-with-cdn"></a>Dimensionar Transmissão em Fluxo com CDN

Consulte os seguintes artigos:

- [Visão geral da CDN](../../cdn/cdn-overview.md)
- [Dimensionar Transmissão em Fluxo com CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Faça perguntas e obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Ver também

[Empacotamento dinâmico](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>Passos seguintes

[Gerir pontos finais de transmissão em fluxo](manage-streaming-endpoints-howto.md)
