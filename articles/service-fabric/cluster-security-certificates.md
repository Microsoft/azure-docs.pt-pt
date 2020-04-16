---
title: Autenticação baseada em certificado X.509 num cluster de tecido de serviço
description: Saiba sobre a autenticação baseada em certificados em clusters de Tecidode Serviço e como detetar, mitigar e corrigir problemas relacionados com certificados.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429671"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Autenticação baseada em certificadoX.509 em clusters de tecido de serviço

Este artigo complementa a introdução à segurança do [cluster Service Fabric,](service-fabric-cluster-security.md)e vai para os detalhes da autenticação baseada em certificados em clusters de Tecido de Serviço. Assumimos que o leitor está familiarizado com conceitos fundamentais de segurança, e também com os controlos que o Service Fabric expõe para controlar a segurança de um cluster.  

Tópicos abordados sob este título:

* Fundamentos de autenticação baseados em certificados
* Identidades e respetivos papéis
* Regras de configuração de certificados
* Resolução de problemas e perguntas frequentes

## <a name="certificate-based-authentication-basics"></a>Fundamentos de autenticação baseados em certificados
Como um breve refrescante, em segurança, um certificado é um instrumento destinado a ligar informações sobre uma entidade (o sujeito) à sua posse de um par de chaves criptográficas assimétricas, constituindo assim uma construção central da criptografia de chaves públicas. As chaves representadas por um certificado podem ser utilizadas para proteger dados ou para provar a identidade dos porta-chaves; Quando utilizado em conjunto com um sistema de infraestrutura de chaves públicas (PKI), um certificado pode representar traços adicionais do seu sujeito, tais como a propriedade de um domínio de internet, ou certos privilégios que lhe são concedidos pelo emitente do certificado (conhecido como Autoridade de Certificação, ou CA). Uma aplicação comum de certificados está a apoiar o protocolo criptográfico de Segurança da Camada de Transporte (TLS), permitindo comunicações seguras sobre uma rede informática. Especificamente, o cliente e o servidor utilizam certificados para garantir a privacidade e integridade da sua comunicação, bem como para realizar a autenticação mútua.

No Tecido de Serviço, a camada fundamental de um cluster (Federação) também se baseia em TLS (entre outros protocolos) para alcançar uma rede fiável e segura de nós participantes. As ligações ao cluster através do Serviço Fabric Client APIs utilizam tLS também para proteger o tráfego, bem como para estabelecer as identidades das partes. Especificamente, quando utilizado para autenticação em Tecido de Serviço, pode ser utilizado um certificado para provar as seguintes alegações: a) o apresentador da credencial do certificado tem a posse da chave privada b do certificado) o haxixe SHA-1 do certificado corresponde a uma declaração incluída na definição de cluster, ou c) o distinto Nome Comum do Sujeito do certificado corresponde a uma declaração incluída na definição de cluster , e o emitente do certificado é conhecido ou de confiança.

Na lista acima, o 'b' é coloquialmente conhecido como "penprint fix"; neste caso, a declaração refere-se a um certificado específico e a força do regime de autenticação assenta na premissa de que é computacionalmente inviável falsificar um certificado que produz o mesmo valor de haxixe que outro, embora ainda seja um objeto válido e bem formado em todos os outros aspetos. O ponto c representa uma forma alternativa de declarar um certificado, em que a resistência do regime assenta na combinação do objeto do certificado e da autoridade emissora. Neste caso, a declaração refere-se a uma classe de certificados - quaisquer dois certificados com as mesmas características são considerados totalmente equivalentes. 

As seguintes secções explicarão em profundidade como o tempo de funcionar do Tecido de Serviço utiliza e valida certificados para garantir a segurança do cluster.

## <a name="identities-and-their-respective-roles"></a>Identidades e respetivos papéis
Antes de mergulhar nos detalhes da autenticação ou de assegurar canais de comunicação, é importante enumerar os intervenientes participantes e as funções correspondentes que desempenham num cluster:
- o tempo de funcionamento do Tecido de Serviço, referido como 'sistema': o conjunto de serviços que fornecem as abstrações e a funcionalidade que representam o cluster. Ao referirmo-nos à comunicação in-cluster entre instâncias do sistema, usaremos o termo "identidade de cluster"; ao referirmo-nos ao cluster como destinatário/alvo de tráfego de fora do cluster, usaremos o termo "identidade do servidor".
- aplicações hospedadas, designadas por «aplicações»: código fornecido pelo proprietário do cluster, que é orquestrado e executado no cluster
- clientes: entidades autorizadas a ligar e executar funcionalidades num cluster, de acordo com a configuração do cluster. Distinguimos entre dois níveis de privilégios - "utilizador" e "administrador", respectivamente. Um cliente 'utilizador' limita-se principalmente a operações de leitura (mas nem todas as funcionalidades de leitura), enquanto um cliente 'administrador' tem acesso ilimitado à funcionalidade do cluster. (Para mais detalhes, consulte as [funções](service-fabric-cluster-security-roles.md)de segurança num cluster de tecido de serviço .)
- (Apenas azure) os serviços de Tecido de Serviço que orquestram e expõem controlos para o funcionamento e gestão de clusters de tecidode serviço, referidos simplesmente como "serviço". Dependendo do ambiente, o 'serviço' pode referir-se ao Fornecedor de Recursos de Tecido sinuoso do Serviço Azure, ou a outros Fornecedores de Recursos detidos e operados pela equipa de Tecidos de Serviço.

Num cluster seguro, cada uma destas funções pode ser configurada com a sua própria identidade distinta, declarada como o emparelhamento de um nome de papel predefinido e a sua credencial correspondente. Service Fabric suporta declarações de credenciais como certificados ou diretor de serviço baseado em domínios. (As identidades baseadas no Windows/Kerberos também são suportadas, mas estão fora do âmbito deste artigo; consulte a [segurança baseada no Windows em clusters de Tecidos](service-fabric-windows-cluster-windows-security.md)de Serviço .) Nos clusters Azure, as funções de cliente também podem ser declaradas como [identidades baseadas em Diretórios Ativos azure.](service-fabric-cluster-creation-setup-aad.md)

Tal como aludido acima, o tempo de execução do Tecido de Serviço define dois níveis de privilégio num cluster: 'administrador' e 'utilizador'. Um cliente administrador e um componente de "sistema" funcionariam ambos com privilégios de administrador, pelo que são indistinguíveis uns dos outros. Ao estabelecer uma ligação dentro/ao cluster, um chamador autenticado será concedido pelo tempo de execução do Tecido de Serviço uma das duas funções como base para a autorização subsequente. Examinaremos a autenticação em profundidade nas seguintes secções.

## <a name="certificate-configuration-rules"></a>Regras de configuração de certificados
### <a name="validation-rules"></a>Regras de validação
As definições de segurança de um cluster de tecido de serviço descrevem, em princípio, os seguintes aspetos:
- o tipo de autenticação; esta é uma característica imutável do aglomerado em tempo de criação. Exemplos de tais definições são 'ClusterCredentialType', 'ServerCredentialType', e os valores permitidos são 'nenhum', 'x509' ou 'windows'. Este artigo centra-se na autenticação do tipo x509.
- As regras de validação (autenticação); estas definições são definidas pelo proprietário do cluster e descrevem quais as credenciais que devem ser aceites para uma determinada função. Exemplos serão examinados em profundidade imediatamente abaixo.
- Definições utilizadas para ajustar ou alterar subtilmente o resultado da autenticação; exemplos aqui incluem bandeiras (de-)restringir a aplicação das listas de revogação de certificados, etc.

> [!NOTE]
> Os exemplos de configuração do cluster fornecidos abaixo são excertos do manifesto de cluster no formato XML, como o formato mais digerido que suporta diretamente a funcionalidade Service Fabric descrita neste artigo. As mesmas configurações podem ser expressas diretamente nas representações jSON de uma definição de cluster, seja um manifesto de cluster json autónomo, ou um modelo de manejo de recursos Azure.

Uma regra de validação de certificados compreende os seguintes elementos:
- o papel correspondente: cliente, cliente administrativo (papel privilegiado)
- a credencial aceite para o papel, declarada quer por impressão digital ou por nome comum sujeito

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Declarações de validação de certificados baseadas em impressão digital
No caso das regras de validação baseadas em impressão digital, as credenciais apresentadas por um chamador que solicite uma ligação dentro/ao cluster serão validadas da seguinte forma:
  - a credencial é um certificado válido e bem formado: a sua cadeia pode ser construída, as assinaturas coincidem
  - o certificado é válido (NotBefore <= now < NotAfter)
  - o haxixe SHA-1 do certificado corresponde à declaração, como uma comparação de cordas insensíveis, excluindo todos os espaços brancos

Quaisquer erros de confiança encontrados durante a construção ou validação em cadeia serão suprimidos para declarações baseadas em impressão digital, com exceção dos certificados expirados - embora existam disposições para esse caso também. Especificamente, as falhas relacionadas com: estatuto de revogação desconhecida ou offline, raiz não fidedigna, utilização de chaves inválidas, cadeia parcial são consideradas erros não fatais; a premissa, neste caso, é que o certificado é apenas um envelope para um par-chave - a segurança reside no facto de o proprietário do cluster ter estabelecido medidas para salvaguardar a chave privada.

O seguinte excerto de um manifesto de cluster exemplifica tal conjunto de regras de validação baseadas em impressão digital:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Cada uma das entradas acima refere-se a uma identidade específica descrita anteriormente; cada entrada também permite especificar vários valores, como lista separada de comma de cordas. Neste exemplo, ao validar com sucesso as credenciais de entrada, o apresentador de um certificado com a impressão digital SHA-1 'd5ec... 4264» será concedido o papel de administrador; inversamente, um chamador autenticando com certificado '7c8f... 01b0» será concedida uma função de "utilizador", restrita a operações exclusivamente de leitura. Um chamador de entrada que apresenta um certificado cuja impressão digital é 'abcd... 1234" ou 'ef01... 5678' será aceite como um nó de pares no cluster. Por último, um cliente que se liga a um ponto final de gestão do cluster espera que a impressão digital do certificado do servidor seja 'ef01... 5678'. 

Como mencionado anteriormente, o Service Fabric prevê a aceitação de certificados caducados; a razão é que os certificados têm uma vida útil limitada e, quando declarados por impressão digital (que se refere a uma instância específica de certificado), permitir a caducidade de um certificado resultará na não ligação ao cluster, ou num colapso total do cluster. É muito fácil esquecer ou negligenciar a rotação de um certificado com tampa de impressão digital e, infelizmente, a recuperação de tal situação é difícil.

Para o efeito, o proprietário do cluster pode indicar explicitamente que os certificados auto-assinados declarados por impressão digital serão considerados válidos, da seguinte forma:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Este comportamento não se estende aos certificados emitidos pela AC; Se assim fosse, um certificado caducada revogado e conhecido por ser comprometido poderia tornar-se "válido" logo que deixasse de figurar na lista de revogação do certificado da AC e, por conseguinte, representasse um risco de segurança. Com certificados auto-assinados, o proprietário do cluster é considerado o único responsável pela salvaguarda da chave privada do certificado, o que não é o caso dos certificados emitidos pela AC - o proprietário do cluster pode não estar ciente de como ou quando o seu certificado foi declarado comprometido.

#### <a name="common-name-based-certificate-validation-declarations"></a>Declarações comuns de validação de certificados baseadas em nomes
As declarações comuns baseadas em nomes assumem um dos seguintes formulários:
- nome comum (apenas)
- nome comum sujeito com fixação emitudador

Primeiro, consideremos um excerto de um manifesto de aglomerado que exemplifica ambos os estilos de declaração:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
As declarações referem-se às identidades do servidor e do cluster, respectivamente; note que as declarações baseadas em CN têm as suas próprias secções no manifesto do cluster, separadas da norma "Segurança". Em ambas as declarações, o nome «Nome» representa o nome comum distinto do certificado, e o campo "Valor" representa o emitente esperado:

- no primeiro caso, a declaração afirma que o elemento de nome comum do distinto objeto do certificado de servidor deverá coincidir com a cadeia "server.demo.system.servicefabric.azure-int"; O campo "Valor" vazio denota a expectativa de que a raiz da cadeia de certificados seja confiada no nó/máquina onde o certificado de servidor está a ser validado; no Windows, isto significa que o certificado pode acorrentar-se a qualquer um dos certificados instalados na loja 'Trusted Root CA';
- no segundo caso, a declaração afirma que o apresentador de um certificado é aceite como nó de pares no cluster se o nome comum do certificado corresponder à cadeia "cluster.demo.system.servicefabric.azure-int", *e* a impressão digital do emitente direto do certificado corresponde a uma das entradas separadas da vírvia no campo 'Valor'. (Este tipo de regra é coloquialmente conhecido como "nome comum com fixação emitentes".)

Em qualquer dos casos, a cadeia do certificado é construída e espera-se que esteja livre de erros; isto é, erros de revogação, erros de cadeia parcial ou de confiança inválido sem tempo são considerados fatais, e a validação do certificado falhará. A fixação dos emitentes resultará na consideração do estatuto de "raiz não fidedigna" como um erro não fatal; apesar das aparências, esta é uma forma mais rigorosa de validação, pois permite ao proprietário do cluster restringir o conjunto de emitentes autorizados/aceites para o seu próprio PKI.

Após a construção da cadeia de certificados, é validada contra uma política padrão de TLS/SSL com o sujeito declarado como nome remoto; Um certificado será considerado compatível se o seu nome comum ou qualquer dos seus nomes alternativos de assunto corresponder à declaração do NC do manifesto do cluster. Os wildcards são apoiados neste caso, e a correspondência de cordas é insensível a casos.

(Devemos esclarecer que a sequência acima descrita poderia ser executada para cada tipo de utilização da chave declarada pelo certificado; se o certificado especificar a utilização da chave de autenticação do cliente, a cadeia é construída e avaliada primeiro para uma função de cliente. Em caso de sucesso, a avaliação completa e validação é bem sucedida. Se o certificado não tiver o uso da autenticação do cliente, ou se a validação falhar, o tempo de funcionação do Tecido de Serviço irá construir e avaliar a cadeia para autenticação do servidor.)

Para completar o exemplo, o seguinte excerto ilustra declarando certificados de cliente por nome comum:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

As declarações acima correspondem, respectivamente, às identidades do administrador e do utilizador; a validação de certificados declarados desta forma é exatamente como descrito para os exemplos anteriores, de certificados de cluster e servidor.

> [!NOTE]
> As declarações comuns baseadas em nomes destinam-se a simplificar a rotação e, em geral, a gestão dos certificados de cluster. No entanto, recomenda-se que cumpra as seguintes recomendações para garantir a continuação da disponibilidade e segurança do cluster:
  * preferem o emitente a confiar em raízes fidedignas
  * evitar misturar emitentes de diferentes PKIs
  * Assegurar que todos os emitentes esperados estejam inscritos na declaração de certificado; um emitente desajustado resultará numa validação falhada
  * assegurar que os pontos finais da política de certificados do PKI sejam detetáveis, disponíveis e acessíveis - isto significa que os pontos finais da AIA, CRL ou OCSP são declarados no certificado de folha, e que estão acessíveis para que a construção da cadeia de certificados possa ser concluída.

Juntando tudo, ao receber um pedido de ligação num cluster protegido com certificados X.509, o tempo de funcionamento do Tecido de Serviço utilizará as definições de segurança do cluster para validar as credenciais da parte remota, tal como acima descrito; se for bem sucedido, considera-se que o chamador/parte remota é autenticado. Se a credencial corresponder às múltiplas regras de validação, o tempo de execução concederá ao ouvinte o papel mais privilegiado de qualquer uma das regras correspondidas. 

### <a name="presentation-rules"></a>Regras de apresentação
A secção anterior descrevia como funciona a autenticação num cluster seguro de certificado; esta secção explicará como o próprio tempo de funcionação do Tecido de Serviço descobre e carrega os certificados que utiliza para a comunicação em cluster; chamamos a estas as regras de "apresentação".

Tal como acontece com as regras de validação, as regras de apresentação especificam um papel e a declaração credencial associada, expressa quer por impressão digital, quer pelo nome comum. Ao contrário das regras de validação, as declarações comuns baseadas em nomes não dispõem de disposições relativas à fixação de emitentes; isto permite uma maior flexibilidade, bem como um melhor desempenho. As regras de apresentação são declaradas na secção (s) do manifesto do cluster, para cada tipo de nó distinto; as definições são separadas das secções de segurança do cluster para permitir que cada tipo de nó tenha a sua configuração completa numa única secção. Nos clusters Azure Service Fabric, as declarações de certificado do tipo nó predefinidos às respetivas definições na secção de Segurança da definição do cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Declarações de apresentação de certificados baseadas em impressão digital
Como descrito anteriormente, o tempo de execução do Tecido de Serviço distingue entre o seu papel como par de outros nós no cluster, e como servidor para operações de gestão de clusters. Em princípio, estas configurações podem ser configuradas de forma distinta, mas na prática tendem a alinhar-se. Para o resto deste artigo, assumiremos que as configurações correspondem à simplicidade.

Consideremos o seguinte excerto de um manifesto de aglomerado:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
O elemento 'ClusterCertificate' demonstra o esquema completo, incluindo parâmetros opcionais ('X509FindValueSecondary') ou aqueles com predefinições adequadas ('X509StoreName'); as outras declarações mostram uma forma abreviada. A declaração de certificado de cluster acima indica que as definições de segurança dos nós do tipo 'nt1vm' são inicializadas com certificado 'cc71.. 1984 como as primárias, e a de 49e2. Certificado de 19d6» como secundário; Espera-se que ambos os certificados\'sejam encontrados na loja de certificados LocalMachine My' (ou no caminho equivalente linux, *var/lib/sfcerts).*

#### <a name="common-name-based-certificate-presentation-declarations"></a>Declarações comuns de apresentação de certificados com base em nome
Os certificados do tipo nó também podem ser declarados pelo nome comum do sujeito, conforme exemplificado abaixo:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Para qualquer tipo de declaração, um nó de Tecido de Serviço irá ler a configuração no arranque, localizar e carregar os certificados especificados, e separa-los por ordem descendente do seu atributo NotAfter; Os certificados expirados são ignorados, e o primeiro elemento da lista é selecionado como credencial do cliente para qualquer ligação de Tecido de Serviço tentada por este nó. (Com efeito, a Fabric de Serviço favorece o certificado de caducidade mais distante.)

Note que, para declarações de apresentação baseadas em nome comum, um certificado é considerado compatível se o seu nome comum do assunto for igual ao campo X509FindValue (ou X509FindValueSecondary) da declaração como uma comparação de cordas exata e sensível a casos. Isto contrasta com as regras de validação, que suportam a correspondência wildcard, bem como comparações de cordas insensíveis a casos.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Definições de configuração de certificado diverso
Foi mencionado anteriormente que as definições de segurança de um cluster de Tecido de Serviço também permitem alterar subtilmente o comportamento do código de autenticação. Enquanto o artigo sobre [as definições](service-fabric-cluster-fabric-settings.md) de cluster do Tecido de Serviço representa a lista completa e mais atualizada de definições, vamos expandir o significado de algumas das definições de segurança aqui, para completar a exposição completa na autenticação baseada em certificados. Para cada definição, explicaremos a intenção, o valor/comportamento predefinido, como afeta a autenticação e quais os valores aceitáveis.

Como mencionado, a validação do certificado implica sempre a construção e avaliação da cadeia do certificado. Para os certificados emitidos pela AC, esta chamada aparentemente simples da API da OS implica normalmente várias chamadas de saída para vários pontos finais do PKI emissor, cache de respostas e assim por diante. Dada a prevalência de chamadas de validação de certificados num cluster de Tecido de Serviço, quaisquer problemas nos pontos finais do PKI podem resultar numa menor disponibilidade do cluster ou na avaria total. Embora as chamadas de saída não possam ser suprimidas (ver abaixo na secção DE PERGUNTAS FREQUENTES para mais informações sobre isto), as seguintes definições podem ser usadas para mascarar erros de validação causados por chamadas CRL falhadas.

  * CrlCheckingFlag - sob a secção 'Segurança', corda convertida em UINT. O valor desta definição é utilizado pela Service Fabric para mascarar erros de estado da cadeia de certificados alterando o comportamento da construção em cadeia; é passado para a chamada Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) como o parâmetro 'dwFlags', e pode ser definido para qualquer combinação válida de bandeiras aceites pela função. Um valor de 0 obriga o tempo de execução do Tecido de Serviço a ignorar quaisquer erros de estado de confiança - isto não é recomendado, uma vez que a sua utilização constituiria uma exposição significativa à segurança. O valor predefinido é 0x4000000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando utilizar: para testes locais, com certificados auto-assinados ou certificados de desenvolvimento que não estejam totalmente formados/não possuam uma infraestrutura de chave pública adequada para suportar os certificados. Pode também ser utilizado como mitigação em ambientes com gapped de ar durante a transição entre PKIs.

  Como usar: vamos dar um exemplo que obriga a verificação de revogação apenas para aceder a URLs em cache. Assumindo:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  em seguida, a declaração no manifesto de agrupamento torna-se:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreOErro DeSbotado - sob a secção 'Segurança', booleano com um valor padrão de 'falso'. Representa um atalho para suprimir um estado de erro de construção em cadeia "revogação offline" (ou um estado subsequente de erro de validação da política em cadeia).

  Quando utilizar: testes locais ou com certificados de desenvolvimento não apoiados por um PKI adequado. Utilize como mitigação em ambientes com ar ou quando o PKI é conhecido por ser inacessível.

  Como utilizar:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Outras definições notáveis (todas na secção 'Segurança'):
  * AcceptExpiredPinnedClusterCertificate - discutido na secção dedicada à validação de certificados baseados em impressão digital; permite aceitar certificados de cluster auto-assinados expirados. 
  * CertificateExpirySafetyMargin - intervalo, expresso em minutos anteriores à marca ção do certificado, e durante o qual o certificado é considerado em risco de validade. Serviço Fabric monitoriza certificados de cluster(s) e periodicamente emite relatórios de saúde sobre a sua disponibilidade restante. Dentro do intervalo de "segurança", estes relatórios de saúde são elevados ao estado de "advertência". O padrão é de 30 dias.
  * CertificateHealthReportingInterval - controla a frequência dos relatórios de saúde relativos à validade temporal remanescente dos certificados de cluster. Os relatórios só serão emitidos uma vez por este intervalo. O valor é expresso em segundos, com um padrão de 8 horas.
  * EnforcePrevalidaçãoOnSecurityChanges - boolean, controla o comportamento da atualização do cluster ao detetar alterações das definições de segurança. Se for definido como "verdadeiro", a atualização do cluster tentará garantir que pelo menos um dos certificados correspondentes a qualquer uma das regras de apresentação possa passar uma regra de validação correspondente. A pré-validação é executada antes de as novas definições serem aplicadas a qualquer nó, mas funciona apenas no nó que acolhe a réplica primária do serviço Cluster Manager no momento de dar início à atualização. A partir desta escrita, a definição tem um padrão de 'falso', e será definido como 'verdadeiro' para novos clusters Azure Service Fabric com uma versão de tempo de execução a partir de 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Cenário final (exemplos)
Analisámos as regras de apresentação, as regras de validação e o ajuste das bandeiras, mas como é que isto funciona em conjunto? Nesta secção, trabalharemos através de dois exemplos de ponta a ponta que demonstram como as definições de segurança podem ser alavancadas para atualizações seguras do cluster. Note que esta não se destina a ser uma dissertação abrangente sobre a correta gestão de certificados em Tecido de Serviço, procure um artigo de acompanhante sobre este tema.

A separação das regras de apresentação e validação coloca a questão óbvia (ou preocupação) de saber se podem divergir e quais seriam as consequências. É, de facto, possível que a seleção de um nó de um certificado de autenticação não passe as regras de validação de outro nó. Na verdade, esta discrepância é a principal causa de incidentes relacionados com a autenticação. Ao mesmo tempo, a separação destas regras permite que um cluster continue a funcionar durante uma atualização que altera as definições de segurança do cluster. Considere que, ao aumentar primeiro as regras de validação como um primeiro passo, todos os nós do cluster convergirão nas novas configurações enquanto ainda usam as credenciais atuais. 

Lembre-se que, num cluster de Tecido de Serviço, uma atualização progride através de (até 5) 'domínios de upgrade', ou UDs. Apenas os nós da UD atual estão a ser atualizados/alterados num dado momento, e a atualização só irá avançar para a próxima UD se a disponibilidade do cluster o permitir. (Consulte as atualizações de [cluster sinuosas de tecido](service-fabric-cluster-upgrade.md) de serviço e outros artigos sobre o mesmo tópico para mais detalhes.) As alterações de certificado/segurança são particularmente arriscadas, uma vez que podem isolar os nós do cluster ou deixar o cluster à beira da perda de quórum.

Usaremos a seguinte notação para descrever as definições de segurança de um nó:

Nk: {P:{TP=A}, V:{TP=A}}, onde:
  - 'Nk' representa um nó no domínio de upgrade *k*
  - 'P' representa as atuais regras de apresentação do nó (assumindo que estamos a referir-nos apenas aos certificados de cluster); 
  - 'V' representa as atuais regras de validação do nó (apenas certificado de cluster)
  - 'TP=A' representa uma declaração baseada em impressão digital (TP), sendo 'A' uma impressão digital de certificado
  - «CN=B» representa uma declaração comum baseada em nomes (CN), sendo «B» o nome comum do certificado 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotação de um certificado de cluster declarado por impressão digital
A sequência seguinte descreve como uma atualização de 2 estágios pode ser utilizada para introduzir com segurança um certificado de cluster secundário, declarado por impressão digital; a primeira fase introduz a nova declaração de certificado nas regras de validação, e a segunda fase introduz-a nas regras de apresentação:
  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós partilham uma configuração comum
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - Os nós em UD0 apresentarão certificado A, e aceitarão certificados A ou B; todos os outros nós presentes e aceitar certificado A apenas
  - ao completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, TP=B}} ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - todos os nós presentes certificado A, todos os nós aceitariam o certificado A ou B
      
Neste ponto, o cluster está novamente em equilíbrio, e a segunda fase das definições de segurança de atualização/alteração pode começar:
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - Os nós em UD0 começarão a apresentar B, o que é aceite por qualquer outro nó no cluster.
  - ao completar o último domínio de atualização: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - todos os nós mudaram para o certificado b. Certificado A pode agora ser retirado/removido da definição de cluster com um conjunto subsequente de atualizações.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Converter um cluster de impressões digitais para declarações de certificados com base em nome comum
Da mesma forma, a alteração do tipo de declaração de certificado (da impressão digital para o nome comum) seguirá o mesmo padrão que acima. Note que as regras de validação permitem declarar os certificados de uma determinada função tanto pela impressão digital como pelo nome comum na mesma definição de cluster. Em contraste, porém, as regras de apresentação permitem apenas uma forma de declaração. A propósito, a abordagem segura para converter um certificado de cluster da impressão digital para o nome comum é introduzir primeiro o certificado-alvo pretendido por impressão digital, e, em seguida, mudar essa declaração para um comum baseado em nome. No exemplo seguinte, assumiremos que a impressão digital «A» e o nome comum «B» se referem ao mesmo certificado. 

  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós partilham uma configuração comum, sendo A a impressão digital do certificado primário
  - ao completar o domínio de atualização 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - Os nós em UD0 apresentarão certificado A, e aceitarão certificados com impressão digital A ou nome comum B; todos os outros nós presentes e aceitar certificado A apenas
  - ao completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, CN=B}} ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - todos os nós presentes certificado A, todos os nós aceitariam o certificado A (TP) ou B (CN)

Neste momento podemos proceder à alteração das regras de apresentação com uma atualização subsequente:
  - ao completar o domínio de atualização 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - Os nós em UD0 apresentarão certificado B encontrado pela CN, e aceitarão certificados com impressão digital A ou nome comum B; todos os outros nós presentes e aceitam certificado A apenas, selecionado por impressão digital
  - ao completar o último domínio de atualização: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - todos os nós presentes certificado B encontrados pela CN, todos os nós aceitariam o certificado A (TP) ou B (CN)
    
A conclusão da fase 2 marca igualmente a conversão do cluster para certificados comuns baseados em nomes; as declarações de validação baseadas na impressão digital podem ser removidas numa atualização subsequente do cluster.

> [!NOTE]
> Nos clusters Azure Service Fabric, os fluxos de trabalho acima apresentados são orquestrados pelo Prestador de Recursos de Tecido de Serviço; o proprietário do cluster é ainda responsável pelo fornecimento de certificados no cluster de acordo com as regras indicadas (apresentação ou validação), e é encorajado a realizar alterações em várias etapas.

Num artigo separado abordaremos o tema da gestão e fornecimento de certificados num cluster de Tecidode Serviço.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Resolução de problemas e perguntas frequentes
Embora depurar problemas relacionados com a autenticação em clusters de Tecidos de Serviço não seja fácil, estamos esperançosos que as seguintes dicas e dicas possam ajudar. A maneira mais fácil de iniciar as investigações é examinar os registos do evento Service Fabric nos nós do cluster - não necessariamente apenas aqueles que apresentam sintomas, mas também nós que estão em cima, mas não conseguem ligar-se a um dos seus vizinhos. No Windows, os eventos de importância são tipicamente registados nos canais 'Aplicações e Serviços\Microsoft-ServiceFabric\Admin' ou 'Operacional', respectivamente. Por vezes, pode ser útil permitir a [exploração madeireira CAPI2,](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)para capturar mais detalhes sobre a validação do certificado, recuperação de CRL/CTL etc. (Lembre-se de desativá-lo após completar a repro, pode ser bastante verboso.)

Os sintomas típicos que se manifestam num aglomerado que experimentam problemas de autenticação são: 
  - nós estão para baixo/ciclismo 
  - tentativas de conexão são rejeitadas
  - tentativas de conexão são timing para fora

Cada um dos sintomas pode ser causado por diferentes problemas, e a mesma causa-raiz pode mostrar diferentes manifestações; como tal, vamos apenas listar uma pequena amostra de problemas típicos, com recomendações para corrigi-los. 

* Os nódosos podem trocar mensagens, mas não conseguem ligar-se. Uma possível causa para as tentativas de ligação a terminar é o erro do "certificado não correspondido" - uma das partes de uma ligação Tecido de Serviço-a-Serviço fabrica está a apresentar um certificado que falha as regras de validação do destinatário. Pode ser acompanhado de um dos seguintes erros: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Diagnosticar/investigar mais: em cada um dos nós que tentam a ligação, determinar qual o certificado que está a ser apresentado; examinar o certificado e tentar imitar as regras de validação (verifique se a impressão digital ou a igualdade de nome comum, verifique as impressões digitais do emitente, se especificadas).

  Outro código de erro comum pode ser:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Neste caso, o certificado é declarado por nome comum, e qualquer um dos seguintes aplica-se:
    - os emitentes não são fixados, e o certificado de raiz não é confiável, ou
    - os emitentes são fixados, mas a declaração não inclui a impressão digital do emitente direto deste certificado

* Um nó subiu, mas não pode ligar-se a outros nós; outros nódosos não recebem tráfego de entrada do nó em falência. Neste caso, é possível que o carregamento do certificado falhe no nó local. Procure os seguintes erros:
  - Certificado não encontrado - certifique-se de que os certificados declarados nas regras de apresentação podem ser resolvidos pelo conteúdo do loja de certificados LocalMachine\My (ou conforme especificado). 
    As possíveis causas de falha podem incluir: 
      - caracteres inválidos na declaração de impressão digital
      - o certificado não está instalado
      - o certificado está caducado
      - a declaração de nome comum inclui o prefixo «CN=»
      - a declaração especifica um wildcard e não existe uma correspondência exata na loja cert (declaração: CN=*.mydomain.com, certificado real: CN=server.mydomain.com)

  - credenciais desconhecidas - indica uma chave privada em falta correspondente ao certificado, tipicamente acompanhada de código de erro: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Para remediar, verifique a existência da chave privada; verificar se os SFAdmins têm acesso a 'ler[executar' à chave privada.

  - mau tipo de fornecedor - indica um certificado Crypto New Generation (CNG) ("Microsoft Software Key Storage Provider"); neste momento, o Tecido de Serviço apenas suporta certificados CAPI1. Normalmente acompanhado por código de erro:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Para remediar, recriar o certificado de cluster utilizando um fornecedor CAPI1 (por exemplo, "Microsoft Enhanced RSA and AES Cryptographic Provider") Para mais detalhes sobre fornecedores de criptográficos, consulte a [Understanding Cryptographic Providers](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

