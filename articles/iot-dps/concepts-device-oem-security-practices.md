---
title: Práticas de segurança para fabricantes - Serviço de Provisionamento de Dispositivos Azure IoT
description: Panorama geral práticas de segurança comuns para OEMs e fabricantes de dispositivos que preparam dispositivos para se inscreverem no Serviço de Provisionamento de Dispositivos IoT (DPS) do Azure IoT.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527212"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Práticas de segurança para fabricantes de dispositivos Azure IoT
À medida que mais fabricantes libertam dispositivos IoT, é útil identificar orientações em torno de práticas comuns. Este artigo resume as práticas de segurança recomendadas a ter em conta quando fabrica dispositivos para utilização com o Serviço de Provisionamento de Dispositivos Azure IoT (DPS).  

> [!div class="checklist"]
> * Selecionando opções de autenticação do dispositivo
> * Instalação de certificados em dispositivos IoT
> * Integração de um Módulo de Plataforma Fidedigna (TPM) no processo de fabrico

## <a name="selecting-device-authentication-options"></a>Selecionando opções de autenticação do dispositivo
O objetivo final de qualquer medida de segurança do dispositivo IoT é criar uma solução IoT segura. Mas questões como limitações de hardware, custos e nível de conhecimento de segurança todos os impactos que escolher. Além disso, a sua abordagem à segurança impacta a forma como os seus dispositivos IoT se ligam à nuvem. Embora existam [vários elementos de segurança IoT](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) a considerar, um elemento chave que cada cliente encontra é o tipo de autenticação a usar. 

Três tipos de autenticação amplamente utilizados são certificados X.509, Módulos de Plataforma Fidedigna (TPM) e chaves simétricas. Enquanto existem outros tipos de autenticação, a maioria dos clientes que constroem soluções no Azure IoT usam um destes três tipos. O resto deste artigo pesquisa prós e contras de usar cada tipo de autenticação.

### <a name="x509-certificate"></a>Certificado X.509
Os certificados X.509 são um tipo de identidade digital que pode usar para autenticação. A norma de certificado X.509 está documentada no [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). Em Azure IoT, existem duas formas de autenticar certificados:
- Impressão digital. Um algoritmo de impressão digital é executado em um certificado para gerar uma cadeia hexadecimal. A corda gerada é um identificador único para o certificado. 
- Autenticação da AC com base numa cadeia completa. Uma cadeia de certificados é uma lista hierárquica de todos os certificados necessários para autenticar um certificado de entidade final (EE). Para autenticar um certificado EE, é necessário autenticar cada certificado da cadeia, incluindo uma CA de raiz fidedigna. 

Prós para X.509:
- X.509 é o tipo de autenticação mais seguro suportado em Azure IoT.
- X.509 permite um alto nível de controlo para fins de gestão de certificados.
- Muitos fornecedores estão disponíveis para fornecer soluções de autenticação baseadas em X.509.

Contras para X.509:
- Muitos clientes podem ter de confiar em fornecedores externos para os seus certificados.
- A gestão de certificados pode ser dispendiosa e aumenta o custo total da solução.
- A gestão do ciclo de vida do certificado pode ser difícil se a logística não for bem pensada. 

### <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)
TPM, também conhecido como [ISO/IEC 11889,](https://www.iso.org/standard/66510.html)é um padrão para gerar e armazenar seguramente chaves criptográficas. TPM também se refere a um dispositivo de E/S virtual ou físico que interage com módulos que implementam a norma. Um dispositivo TPM pode existir como hardware discreto, hardware integrado, um módulo baseado em firmware ou um módulo baseado em software. 

Existem duas diferenças fundamentais entre TPMs e teclas simétricas: 
- As fichas TPM também podem armazenar certificados X.509.
- O atestado de TPM em DPS utiliza a chave de endosso TPM (EK), uma forma de autenticação assimétrica. Com a autenticação assimétrica, uma chave pública é usada para encriptação, e uma chave privada separada é usada para desencriptação. Em contraste, as teclas simétricas utilizam a autenticação simétrica, onde a chave privada é utilizada tanto para encriptação como para desencriptação. 

Prós para TPM:
- Os TPMs estão incluídos como hardware padrão em muitos dispositivos Windows, com suporte incorporado para o sistema operativo. 
- O atestado de TPM é mais fácil de garantir do que a chave simétrica baseada em assinaturas de acesso partilhado (SAS).
- Pode facilmente expirar e renovar, ou rolar, credenciais de dispositivo. DPS enrola automaticamente as credenciais do IoT Hub sempre que um dispositivo TPM é devido para reprovisionamento.

Contras para TPM: 
- As TPMs são complexas e podem ser difíceis de usar. 
- O desenvolvimento de aplicações com TPMs é difícil a menos que tenha um TPM físico ou um emulador de qualidade.
- Poderá ter de redesenhar a placa do seu dispositivo para incluir um TPM no hardware. 
- Se rolar o EK num TPM, destrói a identidade do TPM e cria um novo. Embora o chip físico permaneça o mesmo, tem uma nova identidade na sua solução IoT.

### <a name="symmetric-key"></a>Symmetric key
Com teclas simétricas, a mesma chave é usada para encriptar e desencriptar mensagens. Como resultado, a mesma tecla é conhecida tanto pelo dispositivo como pelo serviço que o autentica. O Azure IoT suporta ligações teclas simétricas baseadas em sasken. A autenticação da chave simétrica requer uma responsabilidade significativa do proprietário para proteger as chaves e alcançar um nível de segurança igual com a autenticação X.509. Se utilizar teclas simétricas, a prática recomendada é proteger as teclas utilizando um módulo de segurança de hardware (HSM).

Prós para chave simétrica:
- A utilização de chaves simétricas é a forma mais simples e mais simples de começar com a autenticação.
- Usar teclas simétricas dinamiza o seu processo porque não há nada extra para gerar. 

Contras para chave simétrica: 
- As teclas simétricas têm um grande esforço para fixar as chaves. A mesma chave é partilhada entre o dispositivo e a nuvem, o que significa que a chave deve ser protegida em dois lugares. Em contrapartida, o desafio com os certificados TPM e X.509 está a provar a posse da chave pública sem revelar a chave privada.
- As chaves simétricas facilitam o cumprimento de más práticas de segurança. Uma tendência comum com teclas simétricas é codificar duramente as teclas não encriptadas nos dispositivos. Embora esta prática seja conveniente, deixa as chaves vulneráveis. Pode atenuar algum risco armazenando de forma segura a chave simétrica no dispositivo. No entanto, se a sua prioridade é, em última análise, a segurança e não a conveniência, utilize certificados X.509 ou TPM para autenticação. 

### <a name="shared-symmetric-key"></a>Chave simétrica partilhada
Há uma variação da autenticação simétrica conhecida como chave simétrica partilhada. Esta abordagem envolve a utilização da mesma chave simétrica em todos os dispositivos. A recomendação é evitar a utilização de teclas simétricas partilhadas nos seus dispositivos. 

Pro para chave simétrica partilhada:
- Simples de implementar e barato de produzir em escala. 

Contras para chave simétrica partilhada: 
- Altamente vulnerável a ataques. O benefício de uma implementação fácil é muito superado pelo risco. 
- Qualquer pessoa pode personificar os seus dispositivos se obtiver a chave partilhada.
- Se depender de uma chave simétrica partilhada que fique comprometida, provavelmente perderá o controlo dos dispositivos. 

### <a name="making-the-right-choice-for-your-devices"></a>Fazendo a escolha certa para os seus dispositivos
Para escolher um método de autenticação, certifique-se de que considera os benefícios e custos de cada abordagem para o seu processo de fabrico único.  Para a autenticação do dispositivo, normalmente há uma relação inversa entre o quão segura é uma determinada abordagem e o quão conveniente é.  

## <a name="installing-certificates-on-iot-devices"></a>Instalação de certificados em dispositivos IoT
Se utilizar certificados X.509 para autenticar os seus dispositivos IoT, esta secção oferece orientações sobre como integrar certificados no seu processo de fabrico. Tens de tomar várias decisões.  Estas incluem decisões sobre variáveis comuns de certificado, quando gerar certificados, e quando instalá-los. 

Se estiver habituado a usar senhas, pode perguntar por que não pode usar o mesmo certificado em todos os seus dispositivos, da mesma forma que poderá usar a mesma palavra-passe em todos os seus dispositivos. Primeiro, usar a mesma senha em todo o lado é perigoso. A prática tem exposto empresas a grandes ataques de DDoS, incluindo aquele que derrubou DNS na costa leste dos EUA há vários anos. Nunca utilize a mesma senha em todo o lado, mesmo com contas pessoais. Segundo, um certificado não é uma senha, é uma identidade única. Uma palavra-passe é como um código secreto que qualquer um pode usar para abrir uma porta num edifício seguro.  É algo que sabes, e podes dar a senha a qualquer um para entrar.  Um certificado é como uma carta de condução com a sua foto e outros detalhes, que você pode mostrar a um guarda para entrar em um edifício seguro. Está ligado a quem tu és.  Desde que o guarda corresponda com precisão às pessoas com cartas de condução, só você pode usar a sua carta de condução (identidade) para entrar. 

### <a name="variables-involved-in-certificate-decisions"></a>Variáveis envolvidas nas decisões de certificados
Considere as seguintes variáveis, e como cada uma impacta o processo de fabrico global. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>De onde vem a raiz do certificado de confiança
Pode ser dispendioso e complexo gerir uma infraestrutura pública chave (PKI).  Especialmente se a sua empresa não tiver qualquer experiência em gerir um PKI. As opções são:
- Use um PKI de terceiros. Pode comprar certificados de assinatura intermédias a um fornecedor de certificados de terceiros. Ou pode usar uma Autoridade de Certificados Privada (AC). 
- Use um PKI auto-gerido. Pode manter o seu próprio sistema PKI e gerar os seus próprios certificados.
- Utilize o serviço de segurança [Azure Sphere.](https://azure.microsoft.com/services/azure-sphere/) Esta opção aplica-se apenas aos dispositivos Azure Sphere. 

#### <a name="where-certificates-are-stored"></a>Quando os certificados são armazenados
Existem alguns fatores que impactam a decisão sobre o local onde os certificados são armazenados. Estes fatores incluem o tipo de dispositivo, margens de lucro esperadas (se você pode pagar armazenamento seguro), capacidades do dispositivo, e tecnologia de segurança existente no dispositivo que você pode ser capaz de usar. Considere as seguintes opções:
- Num módulo de segurança de hardware (HSM). A utilização de um HSM é altamente recomendada. Verifique se o quadro de controlo do seu dispositivo já tem um HSM instalado. Se sabe que não tem um HSM, trabalhe com o seu fabricante de hardware para identificar um HSM que satisfaça as suas necessidades.
- Num local seguro no disco, como um ambiente de execução fidedigno (TEE).
- No sistema de ficheiros local ou numa loja de certificados. Por exemplo, a loja de certificados Do Windows. 

#### <a name="connectivity-at-the-factory"></a>Conectividade na fábrica
A conectividade na fábrica determina como e quando obterá os certificados para instalar nos dispositivos. As opções de conectividade são as seguintes:
- Conectividade. Ter conectividade é o ideal, dinamiza o processo de geração de certificados localmente. 
- Sem conectividade. Neste caso, utilize um certificado assinado de um CA para gerar certificados de dispositivo local e offline. 
- Sem conectividade. Neste caso, pode obter certificados que foram gerados com antecedência. Ou pode usar um PKI offline para gerar certificados localmente.

#### <a name="audit-requirement"></a>Requisito de auditoria
Dependendo do tipo de dispositivos que produz, poderá ter um requisito regulamentar para criar um rasto de auditoria de como as identidades dos dispositivos são instaladas nos seus dispositivos. A auditoria adiciona custos significativos de produção. Então, na maioria dos casos, só fazê-lo se necessário. Se não tiver a certeza se é necessária uma auditoria, consulte o departamento jurídico da sua empresa. As opções de auditoria são: 
- Não é uma indústria sensível. Não é necessária auditoria.
- Indústria sensível. Os certificados devem ser instalados numa sala segura de acordo com os requisitos de certificação de conformidade. Se precisar de um quarto seguro para instalar certificados, provavelmente já sabe como os certificados são instalados nos seus dispositivos. E provavelmente já tem um sistema de auditoria. 

#### <a name="length-of-certificate-validity"></a>Duração da validade do certificado
Tal como a carta de condução, os certificados têm uma data de validade definida quando são criadas. Aqui estão as opções para a duração da validade do certificado:
- Renovação não é necessária.  Esta abordagem utiliza um longo período de renovação, pelo que nunca terá de renovar o certificado durante a vida útil do dispositivo. Embora tal abordagem seja conveniente, também é arriscado.  Pode reduzir o risco utilizando um armazenamento seguro como um HSM nos seus dispositivos. No entanto, a prática recomendada é evitar a utilização de certificados de longa duração.
- Renovação necessária.  Terá de renovar o certificado durante o tempo de vida do dispositivo. A duração da validade do certificado depende do contexto, e você precisará de uma estratégia para a renovação.  A estratégia deve incluir onde você está recebendo certificados, e que tipo de funcionalidade over-the-air seus dispositivos têm que usar no processo de renovação. 

### <a name="when-to-generate-certificates"></a>Quando gerar certificados
As capacidades de conectividade da Internet na sua fábrica terão impacto no seu processo de geração de certificados. Tem várias opções para quando gerar certificados: 

- Certificados pré-carregados.  Alguns fornecedores de HSM oferecem um serviço premium no qual o fornecedor HSM instala certificados para o cliente. Em primeiro lugar, os clientes dão ao fornecedor HSM acesso a um certificado de assinatura. Em seguida, o fornecedor HSM instala certificados assinados por esse certificado de assinatura em cada HSM que o cliente compra. Tudo o que o cliente tem de fazer é instalar o HSM no dispositivo. Embora este serviço acrescente custos, ajuda a agilizar o seu processo de fabrico.  E resolve a questão de quando instalar certificados.
- Certificados gerados pelo dispositivo.  Se os seus dispositivos gerarem certificados internamente, então deve extrair o certificado público X.509 do dispositivo para o inscrever em DPS. 
- Fábrica ligada.  Se a sua fábrica tiver conectividade, pode gerar certificados de dispositivo sempre que precisar.
- Fábrica offline com o seu próprio PKI. Se a sua fábrica não tiver conectividade e estiver a utilizar o seu próprio PKI com suporte offline, pode gerar os certificados quando precisar deles.
- Fábrica offline com PKI de terceiros. Se a sua fábrica não tiver conectividade e estiver a utilizar um PKI de terceiros, tem de gerar os certificados com antecedência. E será necessário gerar os certificados a partir de um local que tenha conectividade. 

### <a name="when-to-install-certificates"></a>Quando instalar certificados
Depois de gerar certificados para os seus dispositivos IoT, pode instalá-los nos dispositivos. 

Se utilizar certificados pré-carregados com um HSM, o processo é simplificado. Depois de o HSM ser instalado no dispositivo, o código do dispositivo pode aceder-lhe. Em seguida, você vai ligar para as APIs do HSM para aceder ao certificado que está armazenado no HSM. Esta abordagem é a mais conveniente para o seu processo de fabrico. 

Se não utilizar um certificado pré-carregado, deve instalar o certificado como parte do seu processo de produção. A abordagem mais simples é instalar o certificado no HSM ao mesmo tempo que pisca a imagem inicial do firmware. O seu processo deve adicionar um passo para instalar a imagem em cada dispositivo. Após este passo, pode executar verificações de qualidade finais e quaisquer outros passos, antes de embalar e enviar o dispositivo. 

Existem ferramentas de software disponíveis que permitem executar o processo de instalação e verificação de qualidade final num único passo. Pode modificar estas ferramentas para gerar um certificado ou retirar um certificado de uma loja de certificados pré-gerada. Em seguida, o software pode instalar o certificado onde precisa instalá-lo. Ferramentas de software deste tipo permitem-lhe executar o fabrico de qualidade de produção em escala. 

Depois de ter certificados instalados nos seus dispositivos, o próximo passo é aprender a inscrever os dispositivos com [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integração de um TPM no processo de fabrico
Se utilizar um TPM para autenticar os seus dispositivos IoT, esta secção oferece orientação. A orientação abrange os dispositivos TPM 2.0 amplamente utilizados que têm suporte de chave de autenticação de mensagem baseado em haxixe (HMAC). A especificação TPM para chips TPM é um padrão ISO que é mantido pelo Grupo de Computação Fidedigna. Para mais informações sobre TPM, consulte as especificações para [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) e [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Tomar posse do TPM
Um passo crítico no fabrico de um dispositivo com um chip TPM é tomar posse do TPM. Este passo é necessário para que possa fornecer uma chave ao proprietário do dispositivo. O primeiro passo é extrair a chave de endosso (EK) do dispositivo. O próximo passo é reclamar a propriedade. A forma como o consegue depende do TPM e do sistema operativo que utiliza. Se necessário, contacte o fabricante TPM ou o desenvolvedor do sistema operativo do dispositivo para determinar como reclamar a propriedade. 

No seu processo de fabrico, pode extrair o EK e reclamar a propriedade em momentos diferentes, o que adiciona flexibilidade. Muitos fabricantes aproveitam esta flexibilidade adicionando um módulo de segurança de hardware (HSM) para aumentar a segurança dos seus dispositivos. Esta secção fornece orientações sobre quando extrair o EK, quando reivindicar a propriedade do TPM, e considerações para integrar estes passos numa linha temporal de fabrico. 

> [!IMPORTANT]
> A seguinte orientação pressupõe que utilize um TPM discreto, firmware ou integrado. Nos locais onde é aplicável, a orientação adiciona notas sobre a utilização de um TPM não discreto ou software. Se utilizar um TPM de software, pode haver passos adicionais que esta orientação não inclui. Os TPMs de software têm uma variedade de implementações que estão fora do âmbito deste artigo.  Em geral, é possível integrar um software TPM na seguinte linha temporal geral de fabrico. No entanto, embora um TPM emulado de software seja adequado para prototipagem e teste, não pode fornecer o mesmo nível de segurança que um TPM discreto, firmware ou TPM integrado. Como prática geral, evite utilizar um software TPM em produção.

### <a name="general-manufacturing-timeline"></a>Cronologia geral do fabrico
A seguinte linha temporal mostra como um TPM passa por um processo de produção e acaba num dispositivo. Cada processo de fabrico é único, e esta linha do tempo mostra os padrões mais comuns. A linha do tempo oferece orientação sobre quando tomar certas ações com as teclas. 

#### <a name="step-1-tpm-is-manufactured"></a>Passo 1: TPM é fabricado
- Se comprar TPMs a um fabricante para uso nos seus dispositivos, consulte se extraem chaves de apoio público (EK_pubs) para si. É útil se o fabricante fornecer a lista de EK_pubs com os dispositivos enviados. 
    > [!NOTE]
    > Pode dar ao fabricante TPM o acesso à sua lista de inscrições utilizando políticas de acesso partilhado no seu serviço de fornecimento.  Esta abordagem permite-lhes adicionar os TPMs à sua lista de inscrições para si.  Mas isso é no início do processo de fabrico, e requer confiança no fabricante de TPM. Faça-o por sua conta e risco. 

- Se fabricar TPMs para vender aos fabricantes de dispositivos, considere dar aos seus clientes uma lista de EK_pubs juntamente com os seus TPMs físicos.  Fornecer aos clientes EK_pubs poupa um passo no seu processo. 
- Se fabricar TPMs para utilizar com os seus próprios dispositivos, identifique qual o ponto mais conveniente para extrair o EK_pub. Pode extrair o EK_pub em qualquer um dos pontos restantes da linha do tempo. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Passo 2: TPM é instalado num dispositivo
Nesta altura do processo de produção, deve saber com que caso DPS o dispositivo será utilizado. Como resultado, pode adicionar dispositivos à lista de inscrições para provisões automatizadas. Para obter mais informações sobre o fornecimento automático de dispositivos, consulte a [documentação DPS](about-iot-dps.md).
- Se ainda não extraiste o EK_pub, agora é uma boa altura para o fazeres. 
- Dependendo do processo de instalação do TPM, este passo é também uma boa altura para tomar posse do TPM. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Passo 3: Dispositivo tem firmware e software instalados
Neste ponto do processo, instale o cliente DPS juntamente com o âmbito de ID e URL global para provisionamento.
- Agora é a última oportunidade de extrair o EK_pub. Se um terceiro instalar o software no seu dispositivo, é uma boa ideia extrair primeiro o EK_pub.
- Este ponto no processo de fabrico é ideal para tomar posse do TPM.  
    > [!NOTE]
    > Se estiver a utilizar um TPM de software, pode instalá-lo agora.  Extrair a EK_pub ao mesmo tempo.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Passo 4: O dispositivo é embalado e enviado para o armazém
Um dispositivo pode ficar sentado num armazém durante 6-12 meses antes de ser implantado. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Passo 5: O dispositivo está instalado no local
Depois de o dispositivo chegar à sua localização final, passa por provisões automatizadas com DPS.

Para obter mais informações, consulte [o provisionamento](about-iot-dps.md#provisioning-process) e [o atestado de TPM](concepts-tpm-attestation.md). 

## <a name="resources"></a>Recursos

Além das práticas de segurança recomendadas neste artigo, o Azure IoT fornece recursos para ajudar a selecionar hardware seguro e criar implementações seguras de IoT: 
- [Recomendações](../iot-fundamentals/security-recommendations.md) de segurança Azure IoT para orientar o processo de implantação. 
- O [Azure Security Center](https://azure.microsoft.com/services/security-center/) oferece um serviço para ajudar a criar implementações IoT seguras. 
- Para obter ajuda na avaliação do seu ambiente de hardware, consulte o papel branco [avaliando a sua Segurança IoT](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Para obter ajuda na seleção de hardware seguro, consulte [o hardware seguro certo para a sua implementação IoT](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 