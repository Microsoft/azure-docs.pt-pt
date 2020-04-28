---
title: Gestão de certificados num cluster de Tecido de Serviço
description: Saiba mais sobre a gestão de certificados num cluster de Tecido de Serviço protegido com certificados X.509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: ecdeb5c9e30c176e2f3525f8efeb861d9210b202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196247"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Gestão de certificados em clusters de tecido de serviço

Este artigo aborda os aspetos de gestão dos certificados utilizados para garantir a comunicação em clusters de tecido de serviço Azure, complementando a introdução à segurança do [cluster Service Fabric,](service-fabric-cluster-security.md) bem como o explicador sobre a [autenticação baseada em certificadox.509 em Tecido](cluster-security-certificates.md)de Serviço. Assumimos que o leitor está familiarizado com conceitos fundamentais de segurança, e também com os controlos que o Service Fabric expõe para configurar a segurança de um cluster.  

Aspetos abrangidos por este título:

* O que é exatamente "gestão de certificados"?
* Funções e entidades envolvidas na gestão de certificados
* A viagem de um certificado
* Mergulho profundo em um exemplo
* Resolução de problemas e perguntas frequentes

Mas primeiro - uma isenção de responsabilidade: este artigo tenta emparelhar o seu lado teórico com exemplos práticos, que requerem, bem, especificidades de serviços, tecnologias, e assim por diante. Uma vez que uma parte considerável do público é interna da Microsoft, vamos referir-nos a serviços, tecnologias e produtos específicos do Microsoft Azure. Sinta-se à vontade para pedir esclarecimentos ou orientações na secção de comentários, onde os detalhes específicos da Microsoft não se aplicam no seu caso.

## <a name="defining-certificate-management"></a>Definição da gestão de certificados
Como vimos no artigo de [companhia,](cluster-security-certificates.md)um certificado é um objeto criptográfico essencialmente ligando um par de chaves assimétricas com atributos descrevendo a entidade que representa. No entanto, é também um objeto "perecível", na medida em que a sua vida útil é finita e é suscetível a compromissos - a divulgação acidental ou uma exploração bem sucedida podem tornar um certificado inútil do ponto de vista da segurança. Isto implica a necessidade de alterar os certificados - rotineiramente ou em resposta a um incidente de segurança. Outro aspeto da gestão (e é um tema inteiro por si só) é a salvaguarda de chaves privadas de certificado, ou de segredos que protegem as identidades das entidades envolvidas na obtenção e fornecimento de certificados. Referimo-nos aos processos e procedimentos utilizados para obter certificados e transportá-los de forma segura (e de forma segura) para onde são necessários como "gestão de certificados". Algumas das operações de gestão - como a inscrição, a definição de políticas e os controlos de autorização - estão fora do âmbito deste artigo. Outros - como o provisionamento, a renovação, a re-chave ou a revogação - estão apenas relacionados com o Tecido de Serviço; no entanto, vamos abordá-los aqui de alguma forma, pois compreender estas operações pode ajudar a assegurar adequadamente o seu cluster. 

O objetivo é automatizar a gestão de certificados tanto quanto possível para garantir a disponibilidade ininterrupta do cluster e oferecer garantias de segurança, dado que o processo é livre de toque sem utilizador. Este objetivo é alcançável atualmente em clusters Azure Service Fabric; o restante do artigo irá primeiro desconstruir a gestão de certificados, e mais tarde concentrar-se-á em permitir a autocapotamento.

Especificamente, os tópicos de âmbito são:
  - pressupostos relacionados com a separação de atribuições entre proprietário e plataforma, no âmbito da gestão dos certificados
  - o longo gasoduto de certificados da emissão ao consumo
  - rotação do certificado - porquê, como e quando
  - O que pode correr mal?

Aspetos como a garantia/gestão de nomes de domínio, a inscrição em certificados ou a criação de controlos de autorização para impor a emissão de certificados estão fora do âmbito deste artigo. Consulte a Autoridade de Registo (RA) do seu serviço favorito de Infraestrutura de Chaves Públicas (PKI). Consumidores internos da Microsoft: contacte a Azure Security.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Funções e entidades envolvidas na gestão de certificados
A abordagem de segurança num cluster de Tecido de Serviço é um caso de "cluster owner declare-lo, Service Fabric runtime enforce-lo". Com isto, significa que quase nenhum dos certificados, chaves ou outras credenciais de identidades que participam no funcionamento de um cluster provêm do próprio serviço; todos são declarados pelo proprietário do cluster. Além disso, o proprietário do cluster é também responsável pelo fornecimento dos certificados ao cluster, renovando-os conforme necessário, e garantindo sempre a segurança dos certificados. Mais especificamente, o proprietário do cluster deve garantir que:
  - Os certificados declarados na secção NodeType do manifesto de cluster podem ser encontrados em cada nó desse tipo, de acordo com as regras de [apresentação](cluster-security-certificates.md#presentation-rules)
  - certificados declarados acima são instalados incluindo as suas chaves privadas correspondentes
  - certificados declarados nas regras de apresentação devem aprovar as regras de [validação](cluster-security-certificates.md#validation-rules) 

A Service Fabric, por seu lado, assume as responsabilidades de:
  - localizar/encontrar certificados que correspondam às declarações na definição de cluster  
  - concessão do acesso às chaves privadas correspondentes às entidades controladas pelo serviço de tecido numa base "necessidade"
  - validando certificados em estrita conformidade com as boas práticas de segurança estabelecidas e a definição de cluster
  - elevação de alertas sobre a caducidade iminente dos certificados ou as falhas na realização dos passos básicos de validação de certificados
  - validando (em certa medida) que os aspetos relacionados com o certificado da definição de cluster são satisfeitos pela configuração subjacente dos anfitriões 

Daí resulta que o encargo de gestão de certificados (como operações ativas) recai exclusivamente sobre o proprietário do cluster. Nas seguintes secções, vamos analisar mais de perto cada uma das operações de gestão, com mecanismos disponíveis e o seu impacto no cluster.

## <a name="the-journey-of-a-certificate"></a>A viagem de um certificado
Revejamos rapidamente a progressão de um certificado da emissão para o consumo no contexto de um cluster de Tecido de Serviço:

  1. Um proprietário de domínio regista com a RA de um PKI um domínio ou assunto que gostaria de associar com certificados subsequentes; os certificados, por sua vez, constituirão provas de propriedade do referido domínio ou sujeito
  2. O proprietário do domínio designa ainda na RA as identidades dos solicitantes autorizados - entidades que têm direito a solicitar a inscrição de certificados com o domínio ou sujeito especificado; no Microsoft Azure, o fornecedor de identidade padrão é o Azure Ative Directory, e os soliciters autorizados são designados pela sua identidade AAD correspondente (ou através de grupos de segurança)
  3. Um solicitador autorizado inscreve-se então num certificado através de um Serviço de Gestão Secreta; no Microsoft Azure, o SMS de eleição é o Azure Key Vault (AKV), que armazena de forma segura e permite a recuperação de segredos/certificados por entidades autorizadas. O AKV também renova/re-tecla o certificado conforme configurado na política de certificados associado. (AkV usa AAD como fornecedor de identidade.)
  4. Um recuperador autorizado - a que nos referiremos como um "agente de provisionamento" - recupera o certificado, incluindo a sua chave privada, a partir do cofre, e instala-o nas máquinas que acolhem o cluster
  5. O serviço de tecido de serviço (em funcionamento elevado em cada nó) concede acesso ao certificado para permitir entidades de Tecido de Serviço; estes são designados por grupos locais, e divididos entre ServiceFabricAdministrators e ServiceFabricAllowedUsers
  6. O tempo de execução do Tecido de Serviço acede e utiliza o certificado para estabelecer a federação, ou para autenticar pedidos de entrada de clientes autorizados
  7. O agente de provisionamento monitoriza o certificado do cofre e aciona o fluxo de provisionamento ao detetar a renovação; posteriormente, o proprietário do cluster atualiza a definição do cluster, se necessário, para indicar a intenção de reverter o certificado.
  8. O agente de provisionamento ou o proprietário do cluster é também responsável pela limpeza/apagamento de certificados não utilizados
  
Para os nossos propósitos, os dois primeiros passos da sequência acima não têm grande relação; a única ligação é que o nome comum do certificado é o nome DNS declarado na definição de cluster.

Estes passos são ilustrados abaixo; Note as diferenças de provisionamento entre certificados declarados por impressão digital e nome comum, respectivamente.

*Fig. 1.* Emissão e fluxo de provisionamento de certificados declarados por impressão digital.
![Certificados de provisionamento declarados por impressão digital][Image1]

*Figo 2.* Emissão e provisão dos certificados declarados pelo nome comum do sujeito.
![Certificados de provisão declarados por nome comum][Image2]

### <a name="certificate-enrollment"></a>Inscrição de certificado
Este tópico é abordado em detalhe na [documentação](../key-vault/create-certificate.md)do Cofre chave; estamos incluindo uma sinopse aqui para continuidade e referência mais fácil. Continuando com o Azure como contexto, e utilizando o Cofre chave Azure como serviço de gestão secreta, um solicitor de certificado autorizado deve ter pelo menos permissões de gestão de certificados no cofre, concedidas pelo proprietário do cofre; O solicitor inscrever-se-ia então num certificado da seguinte forma:
    - cria uma política de certificados no Cofre-Chave Azure (AKV), que especifica o domínio/objeto do certificado, o emitente pretendido, o tipo de chave e o comprimento, a utilização prevista e muito mais; consulte [certificados no Cofre de Chaves Azure](../key-vault/certificate-scenarios.md) para mais detalhes. 
    - cria um certificado no mesmo cofre com a política acima especificada; isto, por sua vez, gera um par chave como objetos de cofre, um pedido de assinatura de certificado assinado com a chave privada, e que é então encaminhado para o emitente designado para a assinatura
    - Uma vez que o emitente (Autoridade de Certificados) responda com o certificado assinado, o resultado é fundido no cofre, e o certificado está disponível para as seguintes operações:
      - em {vaultUri}/certificados/{name}: o certificado incluindo a chave pública e metadados
      - em {vaultUri}/keys/{name}: a chave privada do certificado, disponível para operações criptográficas (embrulhar/desembrulhar, assinar/verificar)
      - em {vaultUri}/secrets/{name}: o certificado que inclui a sua chave privada, disponível para download como um pfx ou ficheiro pem desprotegido Lembre-se que um certificado de cofre é, de facto, uma linha cronológica de instâncias de certificado, partilhando uma política. As versões de certificado serão criadas de acordo com os atributos de vida e renovação da apólice. É altamente recomendável que os certificados de cofre não partilhem nomes de assuntos ou domínios/DNS; pode ser disruptivo num cluster para fornecer instâncias de certificados de certificados de cofre diferentes, com sujeitos idênticos, mas substancialmente diferentes outros atributos, tais como emitente, utilizações-chave, etc.

Neste ponto, existe um certificado no cofre, pronto para consumo. Para a frente:

### <a name="certificate-provisioning"></a>Fornecimento de certificados
Mencionamos um "agente de provisionamento", que é a entidade que recupera o certificado, incluindo a sua chave privada, a partir do cofre e o instala em cada um dos anfitriões do cluster. (Lembre-se que o Tecido de Serviço não disponibiliza certificados.) No nosso contexto, o cluster será hospedado numa coleção de VMs Azure e/ou conjuntos de escala de máquinas virtuais. Em Azure, o fornecimento de um certificado de um cofre para um VM/VMSS pode ser alcançado com os seguintes mecanismos - assumindo, como acima, que o agente de provisionamento foi previamente autorizado a obter permissões no cofre pelo proprietário do cofre: 
  - ad-hoc: um operador recupera o certificado do cofre (como pfx/PKCS #12 ou pem) e instala-o em cada nó
  - como um conjunto de escala de máquina virtual 'secreto' durante a implantação: o serviço Compute recupera, utilizando a sua primeira identidade partidária em nome do operador, o certificado de um cofre habilitado para a implantação de modelos e instala-o em cada nó da balança de máquinas virtuais[(assim);](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates) nota que isto permite o fornecimento de segredos versonizados apenas
  - utilizando a [extensão VM do cofre chave;](../virtual-machines/extensions/key-vault-windows.md) isto permite o fornecimento de certificados utilizando declarações sem versão, com refrescamento periódico dos certificados observados. Neste caso, prevê-se que o VM/VMSS tenha uma [identidade gerida](../virtual-machines/windows/security-policy.md#managed-identities-for-azure-resources), uma identidade que tenha tido acesso ao cofre que contém os certificados observados.

O mecanismo ad-hoc não é recomendado por múltiplas razões, que vão desde a segurança à disponibilidade, e não serão discutidos mais aprofundadamente; para mais detalhes, consulte [os certificados em conjuntos de escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)de máquinas virtuais .

O fornecimento baseado em VMSS/Compute apresenta vantagens de segurança e disponibilidade, mas também apresenta restrições. Requer - por design - declarar certificados como segredos versonizados, o que o torna adequado apenas para clusters protegidos com certificados declarados por impressão digital. Em contrapartida, o fornecimento baseado em extensão VM do cofre chave instalará sempre a versão mais recente de cada certificado observado, o que o torna adequado apenas para clusters protegidos com certificados declarados pelo nome comum do sujeito. Para sublinhar, não utilize um mecanismo de fornecimento automático (como a extensão KVVM) para certificados declarados por exemplo (isto é, por impressão digital) - o risco de perder disponibilidade é considerável.

Podem existir outros mecanismos de provisionamento; os acima são atualmente aceites para clusters Azure Service Fabric.

### <a name="certificate-consumption-and-monitoring"></a>Consumo e monitorização de certificados
Como mencionado anteriormente, o tempo de execução do Tecido de Serviço é responsável pela localização e utilização dos certificados declarados na definição de cluster. O artigo sobre [autenticação baseada em certificados](cluster-security-certificates.md) explicou em detalhe como o Service Fabric implementa as regras de apresentação e validação, respectivamente, e não será revisitado aqui. Vamos analisar a concessão de acesso e autorização, bem como a monitorização.

Recorde-se que os certificados em Tecido de Serviço são utilizados para uma multiplicidade de finalidades, desde a autenticação mútua na camada da federação até à autenticação TLS para os pontos finais de gestão; isto requer que vários componentes ou serviços de sistema tenham acesso à chave privada do certificado. O tempo de execução do Tecido de Serviço verifica regularmente o certificado, procurando correspondências para cada uma das regras de apresentação conhecidas; para cada um dos certificados correspondentes, a chave privada correspondente está localizada, e a sua lista de controlo de acesso discricionário é atualizada para incluir permissões - tipicamente Ler e Executar - concedidas à respetiva identidade que os exija. (Este processo é informalmente referido como "ACLing".) O processo funciona numa cadência de 1 minuto e abrange também certificados de 'aplicação', como os utilizados para encriptar definições ou como certificados de ponto final. O ACLing segue as regras de apresentação, pelo que é importante ter em mente que os certificados declarados por impressão digital e que são atualizados automaticamente sem a atualização de configuração do cluster que se segue não serão acessíveis.    

### <a name="certificate-rotation"></a>Rotação do certificado
Como nota: O IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) define formalmente a [renovação](https://tools.ietf.org/html/rfc3647#section-4.4.6) como a emissão de um certificado com os mesmos atributos que o certificado a ser substituído - o emitente, a chave e a informação públicas do sujeito são preservados, e [a re-chave](https://tools.ietf.org/html/rfc3647#section-4.4.7) como a emissão de um certificado com um novo par-chave, e nenhuma restrição sobre se o emitente pode ou não mudar. Dada a distinção pode ser importante (considere o caso dos certificados declarados pelo nome comum sujeito com o emitente), optaremos pela "rotação" do termo neutro para cobrir ambos os cenários. (Tenha em mente que, quando informalmente utilizado, a «renovação» refere-se, de facto, à re-chave.) 

Vimos anteriormente que o Cofre chave Azure suporta a rotação automática do certificado: a política de certificado associado define o ponto no tempo, seja por dias antes da expiração ou percentagem do tempo total de vida, quando o certificado é rodado no cofre. O agente de provisionamento deve ser invocado após este ponto de tempo e antes da expiração do certificado agora anterior, para distribuir este novo certificado a todos os nós do cluster. O Serviço Tecido ajudará levantando avisos sanitários quando a data de validade de um certificado (e que está atualmente a ser utilizada no cluster) ocorrer mais cedo do que um intervalo pré-determinado. Um agente de provisionamento automático (ou seja, a extensão KeyVault VM), configurado para observar o certificado do cofre, irá periodicamente fazer uma sondagem no cofre, detetar a rotação e recuperar e instalar o novo certificado. O provisionamento feito através da funcionalidade 'segredos' VM/VMSS exigirá que um operador autorizado atualize o VM/VMSS com o KeyVault URI versionizado correspondente ao novo certificado.

Em qualquer dos casos, o certificado rotativo está agora previsto para todos os nós, e descrevemos o mecanismo que a Service Fabric emprega para detetar rotações; vamos examinar o que acontece a seguir - assumindo a rotação aplicada ao certificado de cluster declarado pelo nome comum (tudo aplicável a partir do momento desta escrita, e versão de execução do Tecido de Serviço 7.1.409):
  - para novas ligações dentro, bem como no cluster, o tempo de execução do Tecido de Serviço encontrará e selecionará o certificado de correspondência com a data de validade mais longínmea (a propriedade 'NotAfter' do certificado, muitas vezes abreviada como 'na')
  - As ligações existentes serão mantidas vivas/autorizadas a caducar ou a cessar naturalmente; um manipulador interno terá sido notificado de que um novo jogo existe

Isto traduz-se nas seguintes observações importantes:
  - O certificado de renovação pode ser ignorado se a sua data de validade for mais cedo do que a do certificado atualmente em uso.
  - A disponibilidade do cluster, ou dos pedidos alojados, prevalece sobre a diretiva para rodar o certificado; o cluster irá convergir para o novo certificado eventualmente, mas sem garantias de tempo. Segue-se:
  - Pode não ser imediatamente óbvio para um observador que o certificado rotativo substituiu completamente o seu antecessor; a única forma de garantir que é (para certificados de cluster) reiniciar as máquinas hospedeiras. Note que não é suficiente reiniciar os nós do Tecido de Serviço, uma vez que os componentes do modo kernel que formam ligações de locação num cluster não serão afetados. Note também que o reinício do VM/VMSS pode causar perda temporária de disponibilidade. (Para os certificados de pedido, basta reiniciar apenas as respetivas instâncias de candidatura.)
  - A introdução de um certificado re-chave que não cumpra as regras de validação pode efetivamente quebrar o cluster. O exemplo mais comum disso é o caso de um emitente inesperado: os certificados de cluster são declarados pelo nome comum com o emitente, mas o certificado rotativo foi emitido por um emitente novo/não declarado.     

### <a name="certificate-cleanup"></a>Limpeza de certificados
Neste momento, não existem disposições em Azure para a remoção explícita dos certificados. Muitas vezes, é tarefa não trivial determinar se um determinado certificado está ou não a ser utilizado num dado momento. Isto é mais difícil para os certificados de candidatura do que para os certificados de cluster. O próprio Serviço Fabric, não sendo o agente de provisionamento, não excluirá um certificado declarado pelo utilizador em nenhuma circunstância. Para os mecanismos de provisionamento padrão:
  - Os certificados declarados como segredos VM/VMSS serão provisionados desde que sejam referenciados na definição VM/VMSS, e sejam recuperáveis do cofre (apagar um cofre secreto/certificado falhará as posteriores implantações vM/VMSS; da mesma forma, desativar uma versão secreta no cofre também falhará as implementações VM/VMSS, que referem essa versão secreta)
  - Versões anteriores de certificados provisionados através da extensão KeyVault VM podem ou não estar presentes num nó VM/VMSS. O agente apenas recupera e instala a versão atual, e não remove quaisquer certificados. Reimaging um nó (que normalmente ocorre todos os meses) redefinirá a loja de certificados para o conteúdo da imagem de OS, pelo que as versões anteriores serão implicitamente removidas. Considere-se, no entanto, que a escala de um conjunto de escala de máquina virtual resultará apenas na instalação da versão atual dos certificados observados; não assuma a homogeneidade dos nódosos no que diz respeito aos certificados instalados.   

## <a name="simplifying-management---an-autorollover-example"></a>Simplificando a gestão - um exemplo de autorollover
Descrevemos mecanismos, restrições, delineamos regras e definições intrincadas, e fizemos previsões terríveis de interrupções. Talvez seja altura de mostrar como criar uma gestão automática de certificados para evitar todas estas preocupações. Estamos a fazê-lo no contexto de um cluster Azure Service Fabric a funcionar num conjunto de máquinas virtuais PaaSv2, utilizando o Azure Key Vault para a gestão de segredos e alavancando identidades geridas, da seguinte forma:
  - A validação dos certificados é alterada de impressão digital para fixação de sujeito + emitente: qualquer certificado com um determinado sujeito de um determinado emitente é igualmente confiável
    - Os certificados são matriculados e obtidos a partir de uma loja de confiança (Key Vault), e refrescados por um agente - neste caso, a extensão KeyVault VM
    - O fornecimento de certificados é alterado de tempo de implantação e de versão (tal como feito pela ComputeRP) para pós-implantação e utilização de URIs KeyVault sem versão
    - O acesso ao KeyVault é concedido através de identidades geridas atribuídas pelo utilizador; a identidade dos UA é criada e atribuída à escala de máquina virtual definida durante a implantação
    - Após a implantação, o agente (a extensão KV VM) irá fazer uma sondagem e atualizar os certificados observados em cada nó da balança virtual de máquinas; A rotação do certificado é, portanto, totalmente automatizada, uma vez que a SF irá automaticamente recolher o certificado válido mais distante

A sequência é totalmente scriptável/automatizada e permite uma implementação inicial sem contacto do utilizador de um cluster configurado para autocapotover overrollover de certificado. Passos detalhados são fornecidos abaixo. Usaremos uma mistura de cmdlets PowerShell e fragmentos de modelos json. A mesma funcionalidade é alcançável com todos os meios suportados de interagir com o Azure.

[!NOTE] Este exemplo pressupõe que já existe um certificado no cofre; A inscrição e renovação de um certificado gerido pelo KeyVault requer passos manuais pré-requisitos, tal como descrito anteriormente neste artigo. Para ambientes de produção, utilize certificados geridos pelo KeyVault - um script de amostra específico de um PKI interno da Microsoft está incluído abaixo.
O autorollover do certificado só faz sentido para os certificados emitidos pela AC; a utilização de certificados auto-assinados, incluindo os gerados ao implantar um cluster de tecido de serviço no portal Azure, é absurdo, mas ainda é possível para as implementações locais/desenvolvedores, declarando que a impressão digital do emitente é a mesma que o certificado de folha.

### <a name="starting-point"></a>Ponto de partida
Para a brevidade, assumiremos o seguinte estado de partida:
  - O cluster De tecido de serviço existe e é protegido com um certificado emitido pela AC declarado por impressão digital.
  - O certificado é armazenado em um cofre, e provisionado como um segredo de escala de máquina virtual
  - O mesmo certificado será utilizado para converter o cluster em declarações comuns de certificados baseados em nomes, podendo assim ser validado pelo sujeito e pelo emitente; se não for esse o caso, obtenha o certificado emitido pela AC destinado a este fim, e adicione-o à definição de cluster por impressão digital, tal como [aqui](service-fabric-cluster-security-update-certs-azure.md) explicado.

Aqui está um excerto json de um modelo correspondente a tal estado - note que este omite muitas configurações necessárias, e apenas ilustra os aspetos relacionados com certificados:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

O acima diz essencialmente que ```json [parameters('primaryClusterCertificateTP')] ``` o certificado com ```json [parameters('clusterCertificateUrlValue')] ``` impressão digital e encontrado no KeyVault URI é declarado como o único certificado do cluster, por impressão digital. Em seguida, vamos criar os recursos adicionais necessários para garantir o autorollover do certificado.

### <a name="setting-up-prerequisite-resources"></a>Criação de recursos pré-requisitos
Como mencionado anteriormente, um certificado provisionado como um segredo de escala de máquina virtual é recuperado do cofre pelo serviço Microsoft.Compute Resource Provider, utilizando a sua identidade de primeira parte e em nome do operador de implantação. Para o autorollover, isso mudará - vamos mudar para usar uma identidade gerida, atribuída ao conjunto de escala de máquina virtual, e que é concedida permissões aos segredos do cofre.

Todos os excertos subsequentes devem ser implantados concomitantemente - eles estão listados individualmente para análise e explicações play-by-play.

Primeiro, defina uma identidade atribuída ao utilizador (os valores predefinidos estão incluídos como exemplos) - consulte a [documentação oficial](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity) para informações atualizadas:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Em seguida, conceda esta identidade ao acesso aos segredos do cofre - consulte a [documentação oficial](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy) para informações atuais:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

No próximo passo, nós:
  - atribuir a identidade atribuída ao utilizador ao conjunto de escala de máquina virtual
  - declarar a dependência da escala de máquina virtual na criação da identidade gerida, e no resultado da concessão do acesso ao cofre
  - declarar a extensão KeyVault VM, exigindo que recupere certificados observados no arranque[(documentação oficial)](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)
  - atualizar a definição da extensão VM de tecido de serviço para depender da extensão KVVM e converter o certificado de cluster para o nome comum (estamos a fazer estas alterações num único passo, uma vez que se enquadram no âmbito do mesmo recurso.)

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Note que, embora não explicitamente listado acima, que removemos o URL do certificado de cofre da secção 'OsProfile' do conjunto de escala de máquina virtual.
O último passo é atualizar a definição de cluster para alterar a declaração de certificado de impressão digital para nome comum - aqui estamos também a fixar as impressões digitais do emitente:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Neste ponto, pode executar as atualizações acima mencionadas numa única implementação; por seu lado, o serviço service Fabric Resource Provider dividirá a atualização do cluster em várias etapas, conforme descrito no segmento sobre [a conversão de certificados de cluster de impressão digital para nome comum](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Análise e observações
Esta secção é um catch-all para explicar os passos acima descritos, bem como chamar a atenção para aspetos importantes.

#### <a name="certificate-provisioning-explained"></a>Prestação de certificados, explicado
A extensão KVVM, como agente de provisionamento, funciona continuamente numa frequência pré-determinada. Ao não conseguir obter um certificado observado, continuaria para o próximo na linha, e depois hibernaria até ao ciclo seguinte. A extensão SFVM, como agente de saquedetede cluster, exigirá os certificados declarados antes de o cluster se formar. Isto, por sua vez, significa que a extensão SFVM só pode ser executada após ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` a recuperação bem sucedida dos certificados de cluster, aqui denotados pela cláusula, e pela definição da ```json "requireInitialSync": true``` extensão KeyVaultVM. Isto indica à extensão KVVM que na primeira execução (após a implantação ou um reboot) deve pedalar através dos seus certificados observados até que todos sejam descarregados com sucesso. A definição deste parâmetro em falso, associada a uma falha na recuperação do(s) certificado de cluster resultaria numa falha na implantação do cluster. Inversamente, exigir uma sincronização inicial com uma lista incorreta/inválida de certificados observados resultaria numa falha da extensão KVVM, pelo que, mais uma vez, uma falha na implementação do cluster.  

#### <a name="certificate-linking-explained"></a>Ligação do certificado, explicado
Pode ter reparado na bandeira da extensão KVVM "linkOnRenovation", e no facto de estar definida como falsa. Estamos a abordar aqui em profundidade o comportamento controlado por esta bandeira e as suas implicações no funcionamento de um aglomerado. Note que este comportamento é específico do Windows.

De acordo com a sua [definição:](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows#extension-schema)
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Os certificados utilizados para estabelecer uma ligação TLS são normalmente [adquiridos como cabo](https://docs.microsoft.com/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) através do Fornecedor de Suporte de Segurança do canal S – ou seja, o cliente não acede diretamente à chave privada do próprio certificado. O canal S suporta a reorientação (ligação) das credenciais sob a forma de uma extensão de certificado ([CERT_RENEWAL_PROP_ID](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): se este imóvel for definido, o seu valor representa a impressão digital do certificado de renovação, pelo que o canal S tentará, em vez disso, carregar o certificado ligado. Na verdade, irá percorrer esta lista ligada (e esperemos que acyclic) até que acabe com o certificado 'final' – um sem marca de renovação. Esta funcionalidade, quando utilizada de forma criteriosa, é uma grande mitigação contra a perda de disponibilidade causada por certificados caducados (por exemplo). Noutros casos, pode ser a causa de interrupções que são difíceis de diagnosticar e mitigar. O canal S executa incondicionalmente a travessia de certificados nas suas propriedades de renovação - independentemente do assunto, emitentes ou quaisquer outros atributos específicos que participem na validação do certificado resultante pelo cliente. É possível, com efeito, que o certificado resultante não tenha uma chave privada associada, ou que a chave não tenha sido adled ao seu potencial consumidor. 
 
Se a ligação estiver ativada, a extensão KeyVault VM, ao recuperar um certificado observado do cofre, tentará encontrar certificados correspondentes e existentes de forma a ligá-los através da propriedade de extensão de renovação. A correspondência é (exclusivamente) baseada no Nome Alternativo sujeito (SAN), e funciona como exemplificado abaixo.
Assuma dois certificados existentes, da seguinte forma: A: CN = "Acessórios de Alice", SAN = {"alice.universalexports.com"}, renovação = 'B' B: CN = "Bits de Bob", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, renovação = ''
 
Assuma que um certificado C é recuperado pelo ext KVVM: CN = "Malware de Mallory", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
A lista de SAN está totalmente incluída em C's, e assim A.renovação = C.thumbprint; A lista B's SAN tem uma intersecção comum com C's, mas não está totalmente incluída nela, por isso a renovação de B.permanece vazia.
 
Qualquer tentativa de invocar a AcquireCredentialsHandle (Canal S) neste estado no certificado A acabará por enviar C para a parte remota. No caso do Tecido de Serviço, o [subsistema](service-fabric-architecture.md#transport-subsystem) de transporte de um cluster utiliza o canal S para autenticação mútua, pelo que o comportamento acima descrito afeta diretamente a comunicação fundamental do cluster. Continuando o exemplo acima, e assumindo que A é o certificado de cluster, o que acontece a seguir depende:
  - se a chave privada de C não for ACLd para a conta que fabrica como, vamos ver falhas em adquirir a chave privada (SEC_E_UNKNOWN_CREDENTIALS ou similar)
  - se a chave privada de C estiver acessível, então veremos falhas de autorização devolvidas pelos outros nós (CertificadoNotMatched, não autorizado, etc.) 
 
Em qualquer dos casos, o transporte falha e o aglomerado pode descer; os sintomas variam. Para piorar as coisas, a ligação depende da ordem de renovação – que é ditada pela ordem da lista de certificados observados da extensão KVVM, pelo calendário de renovação no cofre ou mesmo por erros transitórios que alteram a ordem de recuperação.

Para mitigar tais incidentes, recomendamos:
  - não misture as SANs de diferentes certificados de abóbadas; cada certificado de cofre deve servir um propósito distinto, e o seu sujeito e SAN devem refletir que com especificidade
  - incluir o nome comum do assunto na lista SAN (como, literalmente, "CN=<subject common name>")  
  - se incerto, desativar a ligação à renovação dos certificados previstos com a extensão KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Porquê usar uma identidade gerida atribuída ao utilizador? Quais são as implicações de usá-lo?
À medida que surgiu dos snippets json acima, é necessária uma sequência específica das operações/atualizações para garantir o sucesso da conversão e para manter a disponibilidade do cluster. Especificamente, o recurso conjunto de escala de máquina virtual declara e utiliza a sua identidade para recuperar segredos numa única atualização (do ponto de vista do utilizador). A extensão VM de tecido de serviço (que arranca o cluster) depende da conclusão da extensão Da KeyVault VM, que depende da recuperação bem sucedida dos certificados observados. A extensão KVVM utiliza a identidade do conjunto de máquinas virtuais para aceder ao cofre, o que significa que a política de acesso no cofre deve já ter sido atualizada antes da implementação do conjunto de escala de máquina virtual. 

Para eliminar a criação de uma identidade gerida, ou atribuir-lhe a outro recurso, o operador de implantação deve ter a função necessária (ManagedIdentityOperator) na subscrição ou no grupo de recursos, para além das funções necessárias para gerir os outros recursos referenciados no modelo. 

Do ponto de vista da segurança, lembre-se que a máquina virtual (conjunto de escala) é considerada um limite de segurança no que diz respeito à sua identidade Azure. Isto significa que qualquer aplicação hospedada no VM poderia, em princípio, obter um sinal de acesso representativo do VM - os tokens de acesso de identidade geridos são obtidos a partir do ponto final imds não autenticado. Se considerar que o VM é um ambiente partilhado, ou multi-inquilino, então talvez este método de recuperação de certificados de cluster não seja indicado. Trata-se, no entanto, do único mecanismo de provisionamento adequado para o autocapotamento dos certificados.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Resolução de problemas e perguntas frequentes

*P:* Como inscrever-se programáticamente num certificado gerido pelo KeyVault?
*R*: Descubra o nome do emitente na documentação keyVault e, em seguida, substitua-o no script abaixo.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*P:* O que acontece quando um certificado é emitido por um emitente não declarado/não especificado? Onde posso obter a lista exaustiva de emitentes ativos de um dado PKI?
*R*: Se a declaração de certificado especificar as impressões digitais do emitente, e o emitente direto do certificado não estiver incluído na lista de emitentes fixados, o certificado será considerado inválido - independentemente de a sua raiz ser ou não confiada pelo cliente. Por conseguinte, é fundamental assegurar que a lista de emitentes esteja atual/atualizada. A introdução de um novo emitente é um acontecimento raro, devendo ser amplamente divulgado antes de começar a emitir certificados. 

Em geral, um PKI publicará e manterá uma declaração de prática de certificação, de acordo com o IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Entre outras informações, incluirá todos os emitentes ativos. Recuperar esta lista programáticamente pode diferir de um PKI para outro.   

Para os PKIs internos da Microsoft, consulte a documentação interna sobre os pontos finais/SDKs utilizados para recuperar os emitentes autorizados; é da responsabilidade do proprietário do cluster sondar periodicamente esta lista, e garantir que a sua definição de cluster inclui *todos os* emitentes esperados.

*P:* Vários PKIs são suportados? 
*A:* Sim; não pode declarar múltiplas entradas DE CN no manifesto do cluster com o mesmo valor, mas pode listar emitentes de vários PKIs correspondentes ao mesmo NC. Não é uma prática recomendada, e as práticas de transparência dos certificados podem impedir que esses certificados sejam emitidos. No entanto, como meio de migrar de um PKI para outro, este é um mecanismo aceitável.

*P*: E se o atual certificado de cluster não for emitido ca ou não tiver o sujeito pretendido? 
*R*: Obter um certificado com o sujeito pretendido e adicioná-lo à definição do cluster como secundário, por impressão digital. Uma vez concluída a atualização com sucesso, inicie outra atualização de configuração do cluster para converter a declaração de certificado em nome comum. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png

