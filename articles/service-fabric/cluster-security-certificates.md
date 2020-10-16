---
title: Autenticação baseada em certificado X.509 num Cluster de Tecidos de Serviço
description: Saiba mais sobre a autenticação baseada em certificados em clusters de Tecidos de Serviço e como detetar, mitigar e corrigir problemas relacionados com certificados.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 36717f526f88af753f3929d62e84ee65be4320e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259028"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Autenticação baseada em certificados em clusters de tecido de serviço

Este artigo complementa a introdução à segurança do [cluster de tecido de serviço,](service-fabric-cluster-security.md)e vai para os detalhes da autenticação baseada em certificados em clusters de Tecido de Serviço. Assumimos que o leitor está familiarizado com conceitos de segurança fundamentais, e também com os controlos que a Service Fabric expõe para controlar a segurança de um cluster.  

Tópicos abordados sob este título:

* Fundamentos básicos de autenticação baseados em certificados
* Identidades e respetivas funções
* Regras de configuração de certificados
* Resolução de problemas e Perguntas Frequentes

## <a name="certificate-based-authentication-basics"></a>Fundamentos básicos de autenticação baseados em certificados
Como uma breve atualização, em segurança, um certificado é um instrumento destinado a ligar informações relativas a uma entidade (o sujeito) à sua posse de um par de chaves criptográficas assimétricas, constituindo assim uma construção central da criptografia de chave pública. As teclas representadas por um certificado podem ser utilizadas para a proteção de dados ou para comprovar a identidade dos porta-chaves; Quando utilizado em conjunto com um sistema de infraestruturas de chaves públicas (PKI), um certificado pode representar características adicionais do seu sujeito, tais como a propriedade de um domínio de internet, ou certos privilégios que lhe são concedidos pelo emitente do certificado (conhecido como Autoridade de Certificação, ou AC). Uma aplicação comum de certificados está a apoiar o protocolo criptográfico de Segurança da Camada de Transporte (TLS), permitindo comunicações seguras sobre uma rede de computador. Especificamente, o cliente e o servidor utilizam certificados para garantir a privacidade e integridade da sua comunicação, bem como para realizar a autenticação mútua.

No Tecido de Serviço, a camada fundamental de um cluster (Federação) também se baseia no TLS (entre outros protocolos) para alcançar uma rede fiável e segura de nós participantes. As ligações ao cluster através do Service Fabric Client APIs utilizam também o TLS para proteger o tráfego, bem como para estabelecer as identidades das partes. Especificamente, quando utilizado para a autenticação em Tecido de Serviço, pode ser utilizado um certificado para comprovar as seguintes alegações: a) o apresentador da credencial de certificado possui a chave privada do certificado b) o haxixe SHA-1 do certificado corresponde a uma declaração incluída na definição do cluster, ou c) o nome comum do certificado corresponde a uma declaração incluída na definição do cluster , e o emitente do certificado é conhecido ou confiável.

Na lista acima, 'b' é coloquialmente conhecido como 'pinning de impressão digital'; neste caso, a declaração refere-se a um certificado específico e a força do regime de autenticação assenta na premissa de que é computacionalmente inviável falsificar um certificado que produza o mesmo valor de haxixe que outro, sendo ainda um objeto válido e bem formado em todos os outros aspetos. O ponto c representa uma forma alternativa de declarar um certificado, sempre que a força do regime se baseie na combinação do objeto do certificado e da autoridade emissora. Neste caso, a declaração refere-se a uma classe de certificados - quaisquer dois certificados com as mesmas características são considerados totalmente equivalentes. 

As seguintes secções explicarão em profundidade como o tempo de execução do Tecido de Serviço utiliza e valida certificados para garantir a segurança do cluster.

## <a name="identities-and-their-respective-roles"></a>Identidades e respetivas funções
Antes de mergulhar nos detalhes da autenticação ou da segurança dos canais de comunicação, é importante enumerar os intervenientes participantes e os papéis correspondentes que desempenham num cluster:
- o tempo de funcionamento do Tecido de Serviço, referido como "sistema": o conjunto de serviços que fornecem as abstrações e funcionalidades que representam o cluster. Quando nos referimos à comunicação em cluster entre instâncias do sistema, usaremos o termo "identidade de cluster"; quando nos referimos ao cluster como o destinatário/alvo do tráfego de fora do cluster, usaremos o termo "identidade do servidor".
- aplicações acolagens, referidas como "aplicações": código fornecido pelo proprietário do cluster, que é orquestrado e executado no cluster
- clientes: entidades autorizadas a conectar-se e executar a funcionalidade num cluster, de acordo com a configuração do cluster. Distinguimos entre dois níveis de privilégios - 'utilizador' e 'administrador', respectivamente. Um cliente 'utilizador' é restrito principalmente a operações apenas de leitura (mas não apenas a funcionalidade de leitura), enquanto um cliente 'administrador' tem acesso ilimitado à funcionalidade do cluster. (Para mais detalhes, consulte [as funções de Segurança num cluster de tecido de serviço](service-fabric-cluster-security-roles.md).)
- (Apenas) os serviços de Tecido de Serviço que orquestram e expõem controlos para o funcionamento e gestão de clusters de tecidos de serviço, referidos simplesmente como "serviço". Dependendo do ambiente, o 'serviço' pode referir-se ao Fornecedor de Recursos de Tecido de Serviço Azure, ou a outros Fornecedores de Recursos detidos e operados pela equipa de Tecidos de Serviço.

Num agrupamento seguro, cada um destes papéis pode ser configurado com a sua própria identidade distinta, declarada como o emparelhamento de um nome de papel predefinido e a sua credencial correspondente. A Service Fabric suporta a declaração de credenciais como certificados ou principal de serviço baseado em domínios. (As identidades baseadas no Windows/Kerberos também são suportadas, mas estão fora do âmbito deste artigo; consulte a [segurança baseada no Windows em clusters de tecidos de serviço](service-fabric-windows-cluster-windows-security.md).) Nos clusters Azure, as funções dos clientes também podem ser declaradas como [identidades baseadas no Azure Ative Directory](service-fabric-cluster-creation-setup-aad.md).

Como aludiu acima, o tempo de funcionação do Tecido de Serviço define dois níveis de privilégio num cluster: 'administrador' e 'utilizador'. Um cliente administrador e um componente de "sistema" funcionariam ambos com privilégios de "administração", pelo que são indistinguíveis uns dos outros. Após estabelecer uma ligação dentro/ao cluster, um chamador autenticado será concedido pelo Service Fabric runtime uma das duas funções como base para a autorização subsequente. Examinaremos a autenticação em profundidade nas seguintes secções.

## <a name="certificate-configuration-rules"></a>Regras de configuração de certificados
### <a name="validation-rules"></a>Regras de validação
As definições de segurança de um cluster de tecido de serviço descrevem, em princípio, os seguintes aspetos:
- o tipo de autenticação; esta é uma característica imutável do cluster no tempo de criação. Exemplos destas definições são 'ClusterCredentialType', 'ServerCredentialType', e os valores permitidos são 'nenhum', 'x509' ou 'windows'. Este artigo centra-se na autenticação do tipo x509.
- As regras de validação (de autenticação); estas definições são definidas pelo proprietário do cluster e descrevem quais as credenciais que serão aceites para um determinado papel. Exemplos serão examinados em profundidade imediatamente abaixo.
- definições utilizadas para ajustar ou alterar subtilmente o resultado da autenticação; exemplos aqui incluem bandeiras (de-)restrição da aplicação das listas de revogação de certificados, etc.

> [!NOTE]
> Exemplos de configuração de cluster fornecidos abaixo são excertos do manifesto do cluster em formato XML, como o formato mais digerido que suporta diretamente a funcionalidade De Tecido de Serviço descrito neste artigo. As mesmas definições podem ser expressas diretamente nas representações de JSON de uma definição de cluster, seja um manifesto de cluster json autónomo ou um modelo de mangement de recursos Azure.

Uma regra de validação de certificados compreende os seguintes elementos:
- o papel correspondente: cliente, cliente administrativo (função privilegiada)
- a credencial aceite para o papel, declarada quer por impressão digital ou nome comum sujeito

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Declarações de validação de certificados baseados em impressão em polegar
No caso das regras de validação baseadas em impressões digitais, as credenciais apresentadas por um chamador que solicite uma ligação dentro/ao cluster serão validadas da seguinte forma:
  - a credencial é um certificado válido e bem formado: a sua cadeia pode ser construída, as assinaturas correspondem
  - o certificado é válido por tempo (Não Antes da <= agora < NotAfter)
  - o haxixe SHA-1 do certificado corresponde à declaração, como uma comparação de cordas insensível, excluindo todos os espaços brancos

Quaisquer erros de confiança encontrados durante a construção ou validação em cadeia serão suprimidos para declarações baseadas em impressões digitais, com exceção dos certificados caducados - embora existam também disposições para esse caso. Especificamente, as falhas relacionadas com: estatuto de revogação sendo desconhecida ou offline, raiz não fideduga, utilização inválida da chave, cadeia parcial são consideradas erros não fatais; a premissa, neste caso, é que o certificado é apenas um envelope para um par chave - a segurança reside no facto de o proprietário do cluster ter estabelecido em locais medidas para salvaguardar a chave privada.

O seguinte excerto de um manifesto de cluster exemplifica um conjunto de regras de validação baseadas em impressões digitais:

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

Cada uma das entradas acima refere-se a uma identidade específica, tal como descrito anteriormente; cada entrada também permite especificar vários valores, como lista de cordas separadas por vírgula. Neste exemplo, ao validar com sucesso as credenciais de entrada, o apresentador de um certificado com a impressão digital SHA-1 'd5ec... 4264» será concedido o papel de administrador; inversamente, um chamador autenticando com certificado '7c8f... 01b0» será concedida uma função de "utilizador", restrita a operações de leitura única. Um chamador de entrada que apresenta um certificado cuja impressão digital é 'abcd... 1234' ou 'ef01... 5678' será aceite como nó de pares no agrupamento. Por último, um cliente que se ligue a um ponto final de gestão do cluster espera que a impressão digital do certificado do servidor seja 'ef01... 5678'. 

Como mencionado anteriormente, a Service Fabric prevê a aceitação de certificados caducados; a razão é que os certificados têm uma vida útil limitada e, quando declarados por impressão digital (que se refere a uma instância específica do certificado), permitir que um certificado expire resultará em falha de ligação ao cluster, ou num colapso total do cluster. É muito fácil esquecer ou negligenciar a rotação de um certificado com impressão digital e, infelizmente, a recuperação de tal situação é difícil.

Para o efeito, o proprietário do cluster pode explicitamente indicar que os certificados auto-assinados declarados por impressão digital devem ser considerados válidos, da seguinte forma:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Este comportamento não se estende aos certificados emitidos pela AC; se assim fosse, um certificado caducado revogado e conhecido por ser comprometido poderia tornar-se "válido", logo que deixasse de figurar na lista de revogação dos certificados da AC, e assim apresentar um risco para a segurança. Com certificados auto-assinados, o proprietário do cluster é considerado o único responsável pela salvaguarda da chave privada do certificado, o que não é o caso dos certificados emitidos pela AC - o proprietário do cluster pode não ter conhecimento de como ou quando o seu certificado foi declarado comprometido.

#### <a name="common-name-based-certificate-validation-declarations"></a>Declarações comuns de validação de certificados com base em nomes
As declarações comuns baseadas em nomes assumem um dos seguintes formulários:
- nome comum sujeito (apenas)
- nome comum assunto com pinning emitente

Consideremos primeiro um excerto de um manifesto de cluster que exemplifica ambos os estilos de declaração:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
As declarações referem-se às identidades do servidor e do cluster, respectivamente; note que as declarações baseadas na CN têm as suas próprias secções no manifesto do cluster, separadas da norma "Segurança". Em ambas as declarações, o «Nome» representa o nome comum distinto do certificado, sendo o campo "Valor" o emitente esperado:

- no primeiro caso, a declaração indica que o elemento de nome comum do sujeito distinto do certificado do servidor deve corresponder à cadeia "server.demo.system.servicefabric.azure-int"; o campo 'Valor' vazio denota a expectativa de que a raiz da cadeia de certificados seja confiada no nó/máquina onde o certificado do servidor está a ser validado; no Windows, isto significa que o certificado pode acorrentar qualquer um dos certificados instalados na loja 'Trusted Root CA';
- no segundo caso, a declaração indica que o apresentador de um certificado é aceite como nó de pares no agrupamento se o nome comum do certificado corresponder à cadeia "cluster.demo.system.servicefabric.azure-int", *e* a impressão digital do emitente direto do certificado corresponde a uma das entradas separadas em vírgula no campo 'Valor'. (Este tipo de regra é coloquialmente conhecido como "nome comum com pinning emitente".)

Em qualquer dos casos, a cadeia do certificado é construída e espera-se que esteja isenta de erros; ou seja, erros de revogação, erros de fiação parcial ou inválidos são considerados fatais, e a validação do certificado falhará. Fixar os emitentes resultará na consideração do estatuto de "raiz não-fidedinha" como um erro não fatal; apesar das aparências, esta é uma forma de validação mais rigorosa, pois permite ao proprietário do cluster restringir o conjunto de emitentes autorizados/aceites ao seu próprio PKI.

Após a construção da cadeia de certificados, é validada contra uma política padrão TLS/SSL com o sujeito declarado como nome remoto; um certificado será considerado uma correspondência se o seu nome comum ou qualquer um dos seus nomes alternativos objeto corresponderem à declaração cn do manifesto do cluster. Os wildcards são apoiados neste caso, e a correspondência de cordas é insensível a caso.

(Devemos esclarecer que a sequência acima descrita pode ser executada para cada tipo de utilização de chave declarada pelo certificado; se o certificado especificar a utilização da chave de autenticação do cliente, a cadeia é construída e avaliada primeiro para uma função de cliente. Em caso de sucesso, a avaliação completa e validação é bem sucedida. Se o certificado não tiver a utilização da autenticação do cliente, ou a validação falhar, o tempo de execução do Tecido de Serviço construirá e avaliará a cadeia para a autenticação do servidor.)

Para completar o exemplo, o seguinte excerto ilustra a declaração de certificados de cliente por nome comum:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

As declarações acima referidas correspondem, respectivamente, às identidades da administração e dos utilizadores; a validação dos certificados declarados desta forma é exatamente como descrito para os exemplos anteriores, de certificados de cluster e servidor.

> [!NOTE]
> As declarações comuns baseadas em nomes destinam-se a simplificar a rotação e, em geral, a gestão dos certificados de agrupamento. No entanto, recomenda-se aderir às seguintes recomendações para garantir a continuação da disponibilidade e segurança do cluster:
  * preferir pinning emitente a confiar em raízes confiáveis
  * evitar misturar emitentes de diferentes PKIs
  * Assegurar que todos os emitentes esperados estejam listados na declaração do certificado; um emitente desajustado resultará numa validação falhada
  * Assegurar que os pontos finais da política de certificados do PKI sejam detetáveis, disponíveis e acessíveis - isto significa que os pontos finais AIA, CRL ou OCSP são declarados no certificado de folha, e que são acessíveis para que o edifício da cadeia de certificados possa ser concluído.

Atando tudo ao mesmo, ao receber um pedido de ligação num cluster protegido com certificados X.509, o tempo de execução do Tecido de Serviço utilizará as definições de segurança do cluster para validar as credenciais da parte remota, tal como acima descrito; se for bem sucedido, considera-se que o chamador/parte remota é autenticado. Se a credencial corresponder a várias regras de validação, o tempo de execução concederá ao chamador o papel privilegiado mais alto de qualquer uma das regras compatíveis. 

### <a name="presentation-rules"></a>Regras de apresentação
A secção anterior descrevia como funciona a autenticação num cluster protegido por certificados; esta secção explicará como o próprio tempo de funcionaamento do Tecido de Serviço descobre e carrega os certificados que utiliza para a comunicação em cluster; chamamos-lhe as regras de "apresentação".

Tal como as regras de validação, as regras de apresentação especificam uma função e a declaração de credencial associada, expressas quer por impressão digital ou nome comum. Ao contrário das regras de validação, as declarações comuns baseadas em nomes não dispõem de disposições relativas à fixação do emitente; isto permite uma maior flexibilidade, bem como um melhor desempenho. As regras de apresentação são declaradas na secção (s) no (s) do manifesto do cluster, para cada tipo de nó distinto; as definições são divididas das secções de Segurança do cluster para permitir que cada tipo de nó tenha a sua configuração completa numa única secção. Nos clusters de tecido de serviço Azure, as declarações de certificado de tipo de nó por defeito às respetivas definições na secção de Segurança da definição do cluster.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Declarações de apresentação de certificados baseadas em impressão em polegar
Como descrito anteriormente, o tempo de execução do Tecido de Serviço distingue entre o seu papel como par de outros nós no cluster, e como servidor para operações de gestão de clusters. Em princípio, estas configurações podem ser configuradas de forma distinta, mas na prática tendem a alinhar-se. Para o resto deste artigo, assumiremos que as configurações correspondem à simplicidade.

Consideremos o seguinte excerto de um manifesto de cluster:
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
O elemento 'ClusterCertificate' demonstra o esquema completo, incluindo parâmetros opcionais ('X509FindValueSecondary') ou aqueles com predefinição adequada ('X509StoreName'); as outras declarações mostram uma forma abreviada. A declaração do certificado de agrupamento acima indica que as definições de segurança dos nós do tipo 'nt1vm' são inicializadas com o certificado 'cc71.. 1984' como o principal, e o '49e2.. Certificado de 19d6' como secundário; espera-se que ambos os certificados sejam encontrados na loja de certificados LocalMachine \' My' (ou no caminho equivalente Linux, *var/lib/sfcerts).*

#### <a name="common-name-based-certificate-presentation-declarations"></a>Declarações comuns de apresentação de certificados com base em nomes
Os certificados de tipo nó também podem ser declarados por nome comum sujeito, conforme exemplificado abaixo:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Para qualquer tipo de declaração, um nó de Tecido de Serviço lerá a configuração no arranque, localizará e carregará os certificados especificados e classifica-os em ordem descendente do seu atributo NotAfter; os certificados caducados são ignorados, e o primeiro elemento da lista é selecionado como credencial do cliente para qualquer ligação de Tecido de Serviço tentada por este nó. (Com efeito, o Service Fabric favorece o certificado de caducidade mais distante.)

Note que, para declarações de apresentação baseadas em nome comum, um certificado é considerado uma correspondência se o seu nome comum é igual ao campo X509FindValue (ou X509FindValueSecondary) da declaração como uma comparação de cordas sensível a casos e exatos. Isto contrasta com as regras de validação, que suportam a correspondência de wildcard, bem como comparações de cordas insensíveis a casos.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Configurações de configuração de certificados diversos
Foi mencionado anteriormente que as definições de segurança de um cluster de Tecido de Serviço também permitem alterar subtilmente o comportamento do código de autenticação. Enquanto o artigo sobre [as definições do cluster de Tecido de Serviço](service-fabric-cluster-fabric-settings.md) representa a lista completa e mais atualizada de definições, vamos expandir o significado de algumas das definições de segurança aqui, para completar a exposição completa na autenticação baseada em certificados. Para cada definição, explicaremos a intenção, o valor/comportamento predefinido, como afeta a autenticação e quais os valores aceitáveis.

Como mencionado, a validação do certificado implica sempre a construção e avaliação da cadeia do certificado. Para os certificados emitidos pela AC, esta chamada aparentemente simples da API do SO implica várias chamadas de saída para vários pontos finais do PKI emissor, caching de respostas e assim por diante. Dada a prevalência de chamadas de validação de certificados num cluster de Tecido de Serviço, quaisquer problemas nos pontos finais do PKI podem resultar numa disponibilidade reduzida do cluster, ou numa desagregação total. Embora as chamadas de saída não possam ser suprimidas (ver abaixo na secção FAQ para mais informações sobre esta matéria), as seguintes definições podem ser usadas para mascarar erros de validação causados pela falha das chamadas DE CRL.

  * CrlCheckingFlag - sob a secção 'Security', corda convertida para UINT. O valor desta definição é utilizado pela Service Fabric para mascarar erros de estado da cadeia de certificados alterando o comportamento da construção em cadeia; é passado para a chamada Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) como o parâmetro 'dwFlags', e pode ser definido para qualquer combinação válida de bandeiras aceites pela função. Um valor de 0 obriga o tempo de execução do Tecido de Serviço a ignorar quaisquer erros de estado de confiança - tal não é recomendado, uma vez que a sua utilização constituiria uma exposição significativa à segurança. O valor predefinido é de 0x400000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Quando utilizar: para testes locais, com certificados auto-assinados ou certificados de desenvolvimento que não estejam totalmente formados/não tenham uma infraestrutura chave pública adequada para suportar os certificados. Pode também utilizar como mitigação em ambientes com lacunas de ar durante a transição entre PKIs.

  Como usar: vamos dar um exemplo que força a verificação de revogação apenas para aceder a URLs em cache. Assumindo:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  em seguida, a declaração no manifesto do cluster torna-se:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - na secção 'Security', boolean com um valor padrão de 'falso'. Representa um atalho para suprimir um estado de erro de construção em cadeia de "revogação offline" (ou um subsequente estado de erro de validação da política de cadeia).

  Quando utilizar: testes locais ou com certificados de desenvolvimento não apoiados por um PKI adequado. Utilize como mitigação em ambientes com lacunas de ar ou quando o PKI é conhecido por ser inacessível.

  Como utilizar:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Outras definições notáveis (todas na secção 'Segurança):
  * AcceptExpiredPinnedClusterCertificate - discutido na secção dedicada à validação de certificados baseados em impressão digital; permite aceitar certificados de cluster auto-assinados expirados. 
  * CertificadoExpirySafetyMargin - intervalo, expresso em minutos anteriores ao prazo de tempo do certificado NotAfter, e durante o qual o certificado é considerado em risco de expiração. O Service Fabric monitoriza os certificados de cluster e emite periodicamente relatórios de saúde sobre a sua disponibilidade remanescente. Dentro do intervalo de "segurança", estes relatórios de saúde são elevados ao estado de "advertência". O padrão é de 30 dias.
  * CertificateHealthReportingInterval - controla a frequência dos relatórios de saúde relativos à validade do tempo remanescente dos certificados de cluster. Os relatórios só serão emitidos uma vez por este intervalo. O valor é expresso em segundos, com um defeito de 8 horas.
  * EnforcePrevalidationOnSecurityChanges - boolean, controla o comportamento da atualização do cluster ao detetar alterações de definições de segurança. Se for definido como "verdadeiro", a atualização do cluster tentará garantir que pelo menos um dos certificados correspondentes a qualquer uma das regras de apresentação possa passar uma regra de validação correspondente. A pré-validação é executada antes de as novas definições serem aplicadas a qualquer nó, mas funciona apenas no nó que hospeda a réplica primária do serviço Cluster Manager no momento de iniciar a atualização. A partir desta escrita, a definição tem um padrão de 'falso', e será definida como 'verdadeira' para novos clusters de Tecido de Serviço Azure com uma versão de tempo de execução a partir de 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Cenário de ponta a ponta (exemplos)
Analisámos as regras de apresentação, as regras de validação e as bandeiras, mas como é que isto funciona em conjunto? Nesta secção, vamos trabalhar através de dois exemplos de ponta a ponta que demonstram como as definições de segurança podem ser alavancadas para atualizações seguras do cluster. Note que esta não se destina a ser uma dissertação abrangente sobre a gestão adequada de certificados em Service Fabric, procure um artigo de acompanhante sobre este tema.

A separação das regras de apresentação e validação coloca a questão óbvia (ou preocupação) de saber se podem divergir e quais seriam as consequências. É, de facto, possível que a seleção de um nó de um certificado de autenticação não passe as regras de validação de outro nó. Na verdade, esta discrepância é a principal causa de incidentes relacionados com a autenticação. Ao mesmo tempo, a separação destas regras permite que um cluster continue a funcionar durante uma atualização que altera as definições de segurança do cluster. Considere que, ao aumentar primeiro as regras de validação como um primeiro passo, todos os nós do cluster convergirão para as novas definições enquanto ainda utilizam as credenciais atuais. 

Lembre-se que, num cluster de Tecido de Serviço, uma atualização progride através de (até 5) 'domínios de upgrade', ou UDs. Apenas os nós na UD atual estão a ser atualizados/alterados num dado momento, e a atualização só irá para a próxima UD se a disponibilidade do cluster o permitir. (Consulte [as atualizações do cluster de tecidos](service-fabric-cluster-upgrade.md) de serviço e outros artigos sobre o mesmo tópico para mais detalhes.) As alterações de certificado/segurança são particularmente arriscadas, uma vez que podem isolar os nós do cluster, ou deixar o cluster à beira da perda de quórum.

Usaremos a seguinte notação para descrever as definições de segurança de um nó:

Nk: {P:{TP=A}, V:{TP=A}}, onde:
  - 'Nk' representa um nó no domínio de upgrade *k*
  - 'P' representa as atuais regras de apresentação do nó (assumindo que estamos apenas a referir-nos a certificados de cluster); 
  - 'V' representa as atuais regras de validação do nó (certificado de cluster apenas)
  - 'TP=A' representa uma declaração baseada em impressão digital (TP), sendo 'A' uma impressão digital de certificado
  - «CN=B» representa uma declaração comum baseada em nomes (CN), sendo "B" o nome comum do certificado 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotação de um certificado de cluster declarado por impressão digital
A seguinte sequência descreve como uma atualização de 2 estágios pode ser usada para introduzir com segurança um certificado de cluster secundário, declarado por impressão digital; A primeira fase introduz a nova declaração de certificado nas regras de validação e a segunda fase introduz-a nas regras de apresentação:
  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós partilham uma configuração comum
  - ao completar o domínio de upgrade 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - os nós em UD0 apresentarão o certificado A e aceitarão os certificados A ou B; todos os outros nós presentes e aceitar certificado A apenas
  - após completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - todos os nós apresentam o certificado A, todos os nós aceitariam certificado A ou B
      
Neste ponto, o cluster está novamente em equilíbrio, e a segunda fase das definições de segurança de upgrade/alteração pode começar:
  - após completar o domínio de upgrade 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - os nós em UD0 começarão a apresentar B, que é aceite por qualquer outro nó no cluster.
  - após completar o último domínio de atualização: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - todos os nós mudaram para o certificado B. O certificado A pode agora ser retirado/removido da definição de cluster com um conjunto subsequente de atualizações.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Conversão de um cluster de declarações de certificados com base em polegares para nomes comuns
Da mesma forma, a alteração do tipo de declaração de certificado (da impressão digital para o nome comum) seguirá o mesmo padrão acima. Note que as regras de validação permitem declarar os certificados de uma determinada função tanto pela impressão digital como pelo nome comum na mesma definição de cluster. Em contraste, porém, as regras de apresentação permitem apenas uma forma de declaração. A propósito, a abordagem segura para converter um certificado de cluster da impressão digital para o nome comum consiste em introduzir primeiro o certificado-alvo pretendido por impressão digital e, em seguida, alterar essa declaração para um certificado comum baseado no nome. No exemplo seguinte, partiremos do princípio de que a impressão digital em polegar 'A' e o nome comum do sujeito 'B' referem-se ao mesmo certificado. 

  - estado inicial: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - o cluster está em repouso, todos os nós partilham uma configuração comum, sendo A a impressão digital do certificado primário
  - ao completar o domínio de upgrade 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - os nós em UD0 apresentarão o certificado A e aceitarão certificados com impressão digital A ou nome comum B; todos os outros nós presentes e aceitar certificado A apenas
  - após completar o último domínio de atualização: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - todos os nós apresentam o certificado A, todos os nós aceitariam o certificado A (TP) ou B (CN)

Neste momento podemos proceder à alteração das regras de apresentação com uma atualização subsequente:
  - ao completar o domínio de upgrade 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - os nós em UD0 apresentarão o certificado B encontrado pela CN e aceitam certificados com impressão digital A ou nome comum B; todos os outros nós presentes e aceitam o certificado A apenas, selecionado por impressão digital
  - após completar o último domínio de atualização: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - todos os nós apresentam certificado B encontrado por CN, todos os nós aceitariam o certificado A (TP) ou B (CN)
    
A conclusão da fase 2 marca igualmente a conversão do cluster em certificados comuns baseados em nomes; as declarações de validação baseadas em impressão digital podem ser removidas numa atualização subsequente do cluster.

> [!NOTE]
> Nos clusters de tecidos de serviço Azure, os fluxos de trabalho acima apresentados são orquestrados pelo Fornecedor de Recursos de Tecido de Serviço; o proprietário do cluster é ainda responsável pelo fornecimento de certificados no cluster de acordo com as regras indicadas (apresentação ou validação), e é encorajado a realizar alterações em várias etapas.

Num artigo separado abordaremos o tema da gestão e fornecimento de certificados num cluster de Tecidos de Serviço.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Resolução de problemas e Perguntas Frequentes
Embora depurar problemas relacionados com a autenticação em clusters de Tecidos de Serviço não seja fácil, esperamos que as seguintes dicas e dicas possam ajudar. A maneira mais fácil de iniciar investigações é examinar os registos de eventos do Service Fabric nos nós do cluster - não necessariamente apenas aqueles que mostram sintomas, mas também nós que estão em cima mas não são capazes de se conectar a um dos seus vizinhos. No Windows, os eventos de importância são normalmente registados nos canais 'Aplicações e Serviços\Microsoft-ServiceFabric\Admin' ou 'Operational', respectivamente. Por vezes, pode ser útil permitir a [registo do CAPI2,](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)captar mais detalhes sobre a validação do certificado, a recuperação de CRL/CTL, etc.( Lembre-se de desativá-lo após completar a repro, pode ser bastante verboso.)

Os sintomas típicos que se manifestam num aglomerado com problemas de autenticação são: 
  - nós estão para baixo/ciclismo 
  - tentativas de conexão são rejeitadas
  - tentativas de conexão estão cronometrando fora

Cada um dos sintomas pode ser causado por diferentes problemas, e a mesma causa de raiz pode apresentar diferentes manifestações; como tal, vamos apenas listar uma pequena amostra de problemas típicos, com recomendações para corrigi-los. 

* Os nós podem trocar mensagens, mas não podem ligar-se. Uma possível causa para as tentativas de ligação a cessar é o erro de "certificado não correspondido" - uma das partes numa ligação de Tecido de Serviço-a-Serviço está a apresentar um certificado que não cumpre as regras de validação do destinatário. Pode ser acompanhado de qualquer um dos seguintes erros: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Para diagnosticar/investigar mais: em cada um dos nós que tentam a ligação, determinar qual o certificado que está a ser apresentado; examinar o certificado e tentar imitar as regras de validação (verificar a impressão digital ou a igualdade de nomes comuns, verificar impressões digitais emitentes se especificado).

  Outro código de erro comum de acompanhamento pode ser:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Neste caso, o certificado é declarado com denominação comum, e qualquer um dos seguintes se aplica:
    - os emitentes não são fixados, e o certificado raiz não é confiável, ou
    - os emitentes estão presos, mas a declaração não inclui a impressão digital do emitente direto deste certificado

* Um nó é para cima, mas não pode ligar-se a outros nós; outros nós não recebem tráfego de entrada do nó de falha. Neste caso, é possível que o carregamento do certificado falhe no nó local. Procure os seguintes erros:
  - certificado não encontrado - certifique-se de que os certificados declarados nas regras de apresentação podem ser resolvidos pelo conteúdo da Loja de Certificados Local\My (ou conforme especificado). 
    As possíveis causas para a falha podem incluir: 
      - caracteres inválidos na declaração de impressão digital
      - o certificado não está instalado
      - o certificado está caducado
      - a declaração de nome comum inclui o prefixo 'CN='
      - a declaração especifica um wildcard e não existe uma correspondência exata na loja cert (declaração: CN=*.mydomain.com, certificado real: CN=server.mydomain.com)

  - credenciais desconhecidas - indica uma chave privada em falta correspondente ao certificado, normalmente acompanhada de código de erro: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Para remediar, verifique a existência da chave privada; verificar se os SFAdmins têm acesso "read/execute" à chave privada.

  - tipo de mau fornecedor - indica um certificado Crypto New Generation (CNG) ("Microsoft Software Key Storage Provider"); neste momento, a Service Fabric suporta apenas certificados CAPI1. Normalmente acompanhado por código de erro:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Para remediar, recosta o certificado de cluster utilizando um provedor CAPI1 (por exemplo, "Microsoft Enhanced RSA e AES Cryptographic Provider"). Para obter mais detalhes sobre os fornecedores de criptomoedas, consulte a [Understanding Cryptographic Providers](/windows/win32/seccertenroll/understanding-cryptographic-providers)
