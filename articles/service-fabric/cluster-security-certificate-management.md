---
title: Gestão de certificados num cluster de tecido de serviço
description: Saiba mais sobre a gestão de certificados num cluster de Tecido de Serviço garantido com certificados X.509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: aba681157d71f94914462b8d9fc13b90d4d6b153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653669"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Gestão de certificados em clusters de tecidos de serviço

Este artigo aborda os aspetos de gestão dos certificados utilizados para garantir a comunicação nos clusters de tecidos de serviço Azure, e complementa a introdução à segurança do cluster de tecido de [serviço,](service-fabric-cluster-security.md) bem como o explicador da [autenticação baseada em certificados X.509 em Tecido de Serviço.](cluster-security-certificates.md) Assumimos que o leitor está familiarizado com conceitos de segurança fundamentais, e também com os controlos que a Service Fabric expõe para configurar a segurança de um cluster.  

Aspetos abrangidos por este título:

* O que é exatamente "gestão de certificados"?
* Funções e entidades envolvidas na gestão de certificados
* A jornada de um certificado
* Mergulhar profundamente num exemplo
* Resolução de problemas e Perguntas Frequentes

Mas primeiro - um aviso: este artigo tenta emparelhar o seu lado teórico com exemplos práticos, que requerem, bem, especificidades de serviços, tecnologias, e assim por diante. Uma vez que uma parte considerável do público é interna da Microsoft, vamos referir-nos a serviços, tecnologias e produtos específicos do Microsoft Azure. Sinta-se à vontade para pedir esclarecimentos ou orientações na secção de comentários, onde os detalhes específicos da Microsoft não se aplicam no seu caso.

## <a name="defining-certificate-management"></a>Definição da gestão de certificados
Como vimos no [artigo de acompanhante,](cluster-security-certificates.md)um certificado é um objeto criptográfico essencialmente ligando um par de chaves assimétrica com atributos que descrevem a entidade que representa. No entanto, é também um objeto "perecível", na medida em que a sua vida útil é finita e é suscetível a compromissos - a divulgação acidental ou uma exploração bem sucedida podem tornar um certificado inútil do ponto de vista da segurança. Isto implica a necessidade de alterar os certificados - quer rotineiramente, quer em resposta a um incidente de segurança. Outro aspeto da gestão (e é um tema por si só) é a salvaguarda de chaves privadas de certificado, ou de segredos que protegem as identidades das entidades envolvidas na aquisição e fornecimento de certificados. Referimo-nos aos processos e procedimentos utilizados para obter certificados e transportá-los de forma segura (e segura) para onde são necessários como "gestão de certificados". Algumas das operações de gestão - como a inscrição, a definição de políticas e os controlos de autorização - estão fora do âmbito deste artigo. Outros - como o provisionamento, renovação, re-chave ou revogação - apenas estão incidentalmente relacionados com o Tecido de Serviço; no entanto, vamos endereçá-los aqui em certa medida, pois entender estas operações pode ajudar a proteger corretamente o seu cluster. 

O objetivo é automatizar a gestão de certificados o máximo possível para garantir a disponibilidade ininterrupta do cluster e oferecer garantias de segurança, dado que o processo é livre de toques de utilização. Este objetivo é alcançável atualmente em clusters de tecido de serviço Azure; o restante do artigo irá primeiro desconstruir a gestão dos certificados, e mais tarde concentrar-se-á em permitir a auto-rollover.

Especificamente, os tópicos de âmbito são:
  - pressupostos relacionados com a separação de atribuições entre proprietário e plataforma, no contexto da gestão de certificados
  - o longo oleoduto de certificados da emissão ao consumo
  - rotação do certificado - porquê, como e quando
  - O que pode correr mal?

Aspetos como a garantia/gestão de nomes de domínio, a inscrição em certificados ou a criação de controlos de autorização para impor a emissão de certificados estão fora do âmbito de aplicação deste artigo. Consulte a Autoridade de Registo (RA) do seu serviço de Infraestrutura de Chaves Públicas (PKI) favorito. Consumidores internos da Microsoft: por favor contacte a Azure Security.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Funções e entidades envolvidas na gestão de certificados
A abordagem de segurança num cluster de Tecidos de Serviço é um caso de "proprietário de cluster declara-o, o tempo de execução do Service Fabric aplica-o". Com isto, significamos que quase nenhum dos certificados, chaves ou outras credenciais de identidades que participam no funcionamento de um cluster provêm do próprio serviço; todos eles são declarados pelo proprietário do cluster. Além disso, o proprietário do cluster é também responsável pela disponibilização dos certificados no cluster, renovando-os conforme necessário, e garantindo a segurança dos certificados em todos os momentos. Mais especificamente, o proprietário do cluster deve assegurar que:
  - os certificados declarados na secção NodeType do manifesto de cluster podem ser encontrados em cada nó desse tipo, de acordo com [as regras](cluster-security-certificates.md#presentation-rules) de apresentação
  - certificados acima declarados são instalados incluindo as suas chaves privadas correspondentes
  - certificados declarados nas regras de apresentação devem passar as [regras de validação](cluster-security-certificates.md#validation-rules) 

A Service Fabric, por seu lado, assume as responsabilidades de:
  - certificados de localização/constatação correspondentes às declarações na definição do cluster  
  - que concede acesso às chaves privadas correspondentes às entidades controladas pelo Service Fabric numa base de "necessidade"
  - validação de certificados em estrita conformidade com as melhores práticas de segurança estabelecidas e a definição de cluster
  - elevação de alertas sobre a caducidade iminente dos certificados, ou falhas na realização das etapas básicas de validação do certificado
  - validando (em certa medida) que os aspetos relacionados com o certificado da definição do cluster sejam cumpridos pela configuração subjacente dos anfitriões 

Daí resulta que os encargos de gestão dos certificados (como operações ativas) recaiem exclusivamente sobre o proprietário do cluster. Nas secções seguintes, vamos analisar de perto cada uma das operações de gestão, com mecanismos disponíveis e o seu impacto no cluster.

## <a name="the-journey-of-a-certificate"></a>A jornada de um certificado
Revejamos rapidamente a progressão de um certificado da emissão para o consumo no contexto de um cluster de Tecidos de Serviço:

  1. Um proprietário de domínio regista com o RA de um PKI um domínio ou sujeito que gostariam de associar com certificados subsequentes; os certificados constituirão, por sua vez, provas de propriedade do referido domínio ou sujeito
  2. O proprietário do domínio também designa na RA as identidades dos solicitadores autorizados - entidades que têm o direito de solicitar a inscrição de certificados com o domínio ou assunto especificados; no Microsoft Azure, o fornecedor de identidade padrão é o Azure Ative Directory, e os solicitadores autorizados são designados pela sua identidade AAD correspondente (ou através de grupos de segurança)
  3. Um solicitador autorizado inscreve-se então num certificado através de um Serviço de Gestão Secreta; no Microsoft Azure, o SMS de eleição é Azure Key Vault (AKV), que armazena e permite a recuperação de segredos/certificados por entidades autorizadas. A AKV também renova/reconssura o certificado tal como configurado na política de certificados associado. (AKV utiliza a AAD como fornecedor de identidade.)
  4. Um recuperador autorizado - a que nos referimos como um "agente de provisionamento" - recupera o certificado, incluindo a sua chave privada, a partir do cofre, e instala-o nas máquinas que acolhem o cluster
  5. O serviço Service Fabric (funcionando elevado em cada nó) concede acesso ao certificado a entidades de Tecido de Serviço; estes são designados por grupos locais, e divididos entre ServiceFabricAdministrators e ServiceFabricAllowedUsers
  6. O Tecido de Serviço acede e utiliza o certificado para estabelecer federação, ou para autenticar pedidos de entrada de clientes autorizados
  7. O agente de provisionamento monitoriza o certificado do cofre e aciona o fluxo de provisionamento aquando da deteção da renovação; posteriormente, o proprietário do cluster atualiza a definição do cluster, se necessário, para indicar a intenção de passar o certificado.
  8. O agente de provisionamento ou o proprietário do cluster são também responsáveis pela limpeza/eliminação de certificados não utilizados
  
Para os nossos propósitos, os dois primeiros passos na sequência acima são em grande parte não relacionados; a única ligação é que o nome comum do sujeito do certificado é o nome DNS declarado na definição de cluster.

Estes passos são ilustrados abaixo; Note-se as diferenças no provisionamento entre certificados declarados por impressão digital e nome comum, respectivamente.

*Fig. 1.* Fluxo de emissão e provisão de certificados declarados por impressão digital.
![Certificados de provisionamento declarados por impressão digital][Image1]

*Fig. 2.* Fluxo de emissão e provisão de certificados declarados por nome comum sujeito.
![Certificados de provisionamento declarados pelo nome comum sujeito][Image2]

### <a name="certificate-enrollment"></a>Inscrição de certificados
Este tópico é abordado em detalhe na [documentação](../key-vault/certificates/create-certificate.md)do Cofre-Chave; estamos incluindo uma sinopse aqui para continuidade e referência mais fácil. Continuando com o Azure como contexto, e utilizando o Azure Key Vault como serviço de gestão secreta, um solicitador de certificado autorizado deve ter pelo menos permissões de gestão de certificados no cofre, concedidas pelo proprietário do cofre; O solicitador inscrever-se-ia então num certificado da seguinte forma:
    - Cria uma política de certificados no Azure Key Vault (AKV), que especifica o domínio/objeto do certificado, o emitente pretendido, o tipo e comprimento da chave pretendidos e muito mais; consulte [certificados no Cofre da Chave Azure](../key-vault/certificates/certificate-scenarios.md) para mais detalhes. 
    - Cria um certificado no mesmo cofre com a política acima especificada; isto, por sua vez, gera um par chave como objetos de abóbada, um pedido de assinatura de certificado assinado com a chave privada, e que é então encaminhado para o emitente designado para a assinatura
    - Uma vez que o emitente (Autoridade de Certificados) responda ao certificado assinado, o resultado é fundido no cofre, estando o certificado disponível para as seguintes operações:
      - em {vaultUri}/certificados/{name}: o certificado que inclui a chave pública e metadados
      - em {vaultUri}/keys/{name}: a chave privada do certificado, disponível para operações criptográficas (embrulhar/desembrulhar, assinar/verificar)
      - em {vaultUri}/secrets/{name}: o certificado incluído na sua chave privada, disponível para download como um ficheiro pfx ou pem desprotegido  
    Recorde-se que um certificado de abóbada é, de facto, uma linha cronológica de casos de certificados, partilhando uma apólice. As versões de certificado serão criadas de acordo com os atributos de vida e renovação da apólice. Recomenda-se vivamente que os certificados de abóbada não partilhem nomes de assuntos ou domínios/DNS; pode ser disruptivo num cluster para a provisionação de instâncias de certificados de diferentes certificados de abóbada, com sujeitos idênticos, mas substancialmente diferentes outros atributos, tais como emitente, usos chave, etc.

Neste momento, existe um certificado no cofre, pronto para consumo. Avante para:

### <a name="certificate-provisioning"></a>Provisionamento de certificados
Mencionamos um "agente de provisionamento", que é a entidade que recupera o certificado, incluindo a sua chave privada, a partir do cofre e o instala em cada um dos anfitriões do cluster. (Lembre-se que o Tecido de Serviço não fornece certificados.) No nosso contexto, o cluster será hospedado numa coleção de VMs Azure e/ou conjuntos de escala de máquinas virtuais. Em Azure, o fornecimento de um certificado de um cofre para um VM/VMSS pode ser alcançado com os seguintes mecanismos - assumindo, como acima, que o agente de provisionamento foi previamente concedido permissões de "obter" no cofre pelo proprietário do cofre: 
  - ad-hoc: um operador recupera o certificado do cofre (como pfx/PKCS #12 ou pem) e instala-o em cada nó
  - como uma balança de máquina virtual definida 'secret' durante a implantação: o serviço Compute recupera, utilizando a sua primeira identidade partidária em nome do operador, o certificado a partir de um cofre ativado por modelo e instala-o em cada nó do conjunto de escala de máquina virtual[(assim );](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates) notar que isto permite o provisionamento de segredos versados apenas
  - utilizando a [extensão VM do cofre chave;](../virtual-machines/extensions/key-vault-windows.md) isto permite o fornecimento de certificados utilizando declarações sem versão, com refrescamento periódico dos certificados observados. Neste caso, espera-se que o VM/VMSS tenha uma [identidade gerida,](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)identidade a que tenha acesso ao cofre(s) contendo os certificados observados.

O mecanismo ad-hoc não é recomendado por múltiplas razões, que vão da segurança à disponibilidade, e não será discutido aqui mais; para mais informações, consulte [certificados em conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

O fornecimento baseado em VMSS/Compute apresenta vantagens de segurança e disponibilidade, mas também apresenta restrições. Requer - por design - declarar certificados como segredos versados, o que o torna adequado apenas para clusters garantidos com certificados declarados por impressão digital. Em contrapartida, o provisionamento baseado em extensões key Vault VM instalará sempre a versão mais recente de cada certificado observado, o que o torna adequado apenas para clusters garantidos com certificados declarados pelo nome comum do sujeito. Para salientar, não utilize um mecanismo de provisionamento de auto-confirmação (como a extensão KVVM) para certificados declarados por exemplo (isto é, por impressão digital) - o risco de perda de disponibilidade é considerável.

Podem existir outros mecanismos de provisionamento; o acima é aceite atualmente para clusters de tecido de serviço Azure.

### <a name="certificate-consumption-and-monitoring"></a>Consumo e monitorização de certificados
Como mencionado anteriormente, o tempo de funcionação do Tecido de Serviço é responsável pela localização e utilização dos certificados declarados na definição de cluster. O artigo sobre [autenticação baseada](cluster-security-certificates.md) em certificados explicou em detalhe como o Service Fabric implementa as regras de apresentação e validação, respectivamente, e não será revisitado aqui. Vamos analisar o acesso e a concessão de permissões, bem como a monitorização.

Recorde-se que os certificados em Tecido de Serviço são utilizados para uma multiplicidade de finalidades, desde a autenticação mútua na camada da federação até à autenticação TLS para os pontos finais de gestão; isto requer que vários componentes ou serviços de sistema tenham acesso à chave privada do certificado. O tempo de funcionação do Tecido de Serviço digitaliza regularmente a loja de certificados, procurando fósforos para cada uma das regras de apresentação conhecidas; para cada um dos certificados correspondentes, a chave privada correspondente está localizada, e a sua lista de controlo de acesso discricionário é atualizada para incluir permissões - tipicamente lidas e executadas - concedidas à respetiva identidade que os requer. (Este processo é informalmente referido como 'ACLing'.) O processo funciona com uma cadência de 1 minuto, e também abrange certificados de "aplicação", como os utilizados para encriptar definições ou como certificados de ponto final. O ACLing segue as regras de apresentação, por isso é importante ter em mente que os certificados declarados por impressão digital e que são auto-protegidos sem a atualização de configuração do cluster subsequente não serão acessíveis.    

### <a name="certificate-rotation"></a>Rotação do certificado
Nota: O IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) define formalmente a [renovação](https://tools.ietf.org/html/rfc3647#section-4.4.6) como a emissão de um certificado com os mesmos atributos que o certificado a ser substituído - o emitente, a chave pública do sujeito e a informação é preservada, e [a re-chave](https://tools.ietf.org/html/rfc3647#section-4.4.7) como emissão de um certificado com um novo par-chave, e nenhuma restrição sobre se o emitente pode ou não mudar. Tendo em conta que a distinção pode ser importante (considere o caso dos certificados declarados pelo nome comum com pinning emitente), optaremos pelo termo neutro "rotação" para cobrir ambos os cenários. (Tenha em mente que, quando informalmente utilizado, a "renovação" se refere, de facto, à re-chave.) 

Vimos anteriormente que o Azure Key Vault suporta a rotação automática do certificado: a política de certificados associados define o ponto no tempo, seja por dias antes da expiração ou percentagem do tempo total, quando o certificado é rodado no cofre. O agente de provisionamento deve ser invocado após este momento e, antes do termo do certificado agora anterior, para distribuir este novo certificado a todos os nós do agrupamento. O Tecido de Serviço ajudará a elevar os avisos sanitários quando a data de validade de um certificado (e que está atualmente a ser utilizado no cluster) ocorrer mais cedo do que um intervalo pré-determinado. Um agente de provisionamento automático (isto é, a extensão KeyVault VM), configurado para observar o certificado do cofre, irá periodicamente sondar o cofre, detetar a rotação e recuperar e instalar o novo certificado. O fornecimento feito através da funcionalidade "segredos" VM/VMSS exigirá que um operador autorizado atualize o VM/VMSS com o KeyVault URI em versão correspondente ao novo certificado.

Em qualquer dos casos, o certificado rotativo é agora a provisionado a todos os nós, e descrevemos o mecanismo que o Service Fabric emprega para detetar rotações; vamos examinar o que acontece a seguir - assumindo a rotação aplicada ao certificado de cluster declarado pelo nome comum do sujeito (todos aplicáveis a partir da hora desta escrita, e versão de execução do tecido de serviço 7.1.409):
  - para novas ligações dentro, bem como no cluster, o tempo de execução do Tecido de Serviço encontrará e selecionará o certificado de correspondência com a data de validade mais distante (a propriedade 'NotAfter' do certificado, muitas vezes abreviada como 'na')
  - as ligações existentes serão mantidas vivas/autorizadas a expirar naturalmente ou de outra forma; um manipulador interno terá sido notificado de que um novo jogo existe

Isto traduz-se nas seguintes observações importantes:
  - O certificado de renovação pode ser ignorado se a sua data de validade for mais cedo do que a do certificado atualmente em uso.
  - A disponibilidade do cluster, ou das aplicações acolhidas, prevalece sobre a diretiva para a rotação do certificado; o cluster irá convergir para o novo certificado eventualmente, mas sem garantias de tempo. Daí:
  - Não pode ser imediatamente óbvio para um observador que o certificado rotativo substituiu completamente o seu antecessor; a única forma de garantir que é (para certificados de cluster) reiniciar as máquinas hospedeiras. Note que não é suficiente reiniciar os nós do Tecido de Serviço, uma vez que os componentes do modo kernel que formam ligações de locação num cluster não serão afetados. Note também que reiniciar o VM/VMSS pode causar perda temporária de disponibilidade. (Para os certificados de pedido, basta reiniciar apenas as respetivas instâncias de aplicação.)
  - A introdução de um certificado reformulado que não cumpra as regras de validação pode efetivamente quebrar o cluster. O exemplo mais comum é o caso de um emitente inesperado: os certificados de agrupamento são declarados por nome comum sujeito com fixação emitente, mas o certificado rotativo foi emitido por um emitente novo/não declarado.     

### <a name="certificate-cleanup"></a>Limpeza de certificados
Neste momento, não existem disposições em Azure para a remoção explícita dos certificados. Muitas vezes, é uma tarefa não trivial determinar se um determinado certificado está ou não a ser utilizado num dado momento. Isto é mais difícil para os certificados de candidatura do que para certificados de cluster. A própria Service Fabric, não sendo o agente de provisionamento, não apagará um certificado declarado pelo utilizador em nenhuma circunstância. Para os mecanismos-padrão de provisionamento:
  - Os certificados declarados como segredos VM/VMSS serão abastados desde que sejam referenciados na definição VM/VMSS, e sejam recuperados do cofre (eliminando um cofre secreto/certificado falhará as subsequentes implantações VM/VMSS; da mesma forma, desativar uma versão secreta no cofre também falhará as implementações VM/VMSS, que referenciam essa versão secreta)
  - As versões anteriores dos certificados a provisionados através da extensão KeyVault VM podem ou não estar presentes num nó VM/VMSS. O agente apenas recupera e instala a versão atual, e não remove quaisquer certificados. A reimaging de um nó (que ocorre normalmente todos os meses) irá redefinir a loja de certificados para o conteúdo da imagem do SO, pelo que as versões anteriores serão implicitamente removidas. Considere-se, no entanto, que o escalonamento de um conjunto de escala de máquina virtual resultará apenas na instalação da versão atual dos certificados observados; Não assuma homogeneidade dos nós no que diz respeito aos certificados instalados.   

## <a name="simplifying-management---an-autorollover-example"></a>Simplificação da gestão - um exemplo de auto-rollover
Descrevemos mecanismos, restrições, delineamos regras e definições intrincadas, e fizemos previsões terríveis de interrupções. Talvez seja altura de mostrar como criar uma gestão automática de certificados para evitar todas estas preocupações. Estamos a fazê-lo no contexto de um cluster de tecido de serviço Azure que funciona num conjunto de escala de máquina virtual PaaSv2, usando o Cofre chave Azure para gestão de segredos e alavancagem de identidades geridas, da seguinte forma:
  - A validação dos certificados é alterada de fixação de impressão digital para objeto + fixação emitente: qualquer certificado com um determinado sujeito de um dado emitente é igualmente confiável
    - Os certificados são matriculados e obtidos a partir de uma loja de confiança (Key Vault), e atualizados por um agente - neste caso, a extensão KeyVault VM
    - O fornecimento de certificados é alterado de tempo de implantação e baseado em versão (como feito pelo ComputeRP) para pós-implantação e utilizando URIs KeyVault sem versão
    - O acesso ao KeyVault é concedido através de identidades geridas atribuídas pelo utilizador; a identidade da UA é criada e atribuída à escala de máquina virtual definida durante a implantação
    - Após a implantação, o agente (a extensão KV VM) irá sondar e atualizar os certificados observados em cada nó do conjunto de escalas de máquina virtual; a rotação do certificado é, portanto, totalmente automatizada, uma vez que a SF irá automaticamente recolher o certificado válido mais distante

A sequência é totalmente scriptável/automatizada e permite uma implantação inicial sem toque do utilizador de um cluster configurado para a auto-inscrição do certificado. Passos detalhados são fornecidos abaixo. Usaremos uma mistura de cmdlets PowerShell e fragmentos de modelos json. A mesma funcionalidade é alcançável com todos os meios suportados de interagir com o Azure.

[!NOTE] Este exemplo pressupõe que já exista um certificado no cofre; A inscrição e renovação de um certificado gerido pela KeyVault requer medidas manuais pré-requisitos, conforme descrito anteriormente neste artigo. Para ambientes de produção, utilize certificados geridos pelo KeyVault - um script de amostra específico para um PKI interno da Microsoft está incluído abaixo.
A cadeca automática de certificados só faz sentido para os certificados emitidos pela AC; A utilização de certificados auto-assinados, incluindo os gerados ao implantar um cluster de Tecidos de Serviço no portal Azure, é absurdo, mas ainda possível para implementações locais/desenvolvedoras, declarando que a impressão digital do emitente é a mesma que o certificado de folha.

### <a name="starting-point"></a>Ponto de partida
Para a brevidade, assumiremos o seguinte estado inicial:
  - O cluster de Tecido de Serviço existe e é protegido com um certificado emitido pela AC declarado por impressão digital.
  - O certificado é armazenado em um cofre, e abastado como um conjunto de escala de máquina virtual conjunto
  - O mesmo certificado será utilizado para converter o cluster em declarações de certificados comuns com base em nomes, podendo assim ser validado por sujeito e emitente; se não for esse o caso, obtenha o certificado emitido pela AC destinado a este fim, e adicione-o à definição de cluster por impressão digital, conforme [explicado aqui](service-fabric-cluster-security-update-certs-azure.md)

Aqui está um excerto json de um modelo correspondente a tal estado - note que isto omite muitas configurações necessárias, e apenas ilustra os aspetos relacionados com o certificado:
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

O acima diz essencialmente que o certificado com impressão digital ```json [parameters('primaryClusterCertificateTP')] ``` e encontrado na KeyVault URI ```json [parameters('clusterCertificateUrlValue')] ``` é declarado como o único certificado do cluster, por impressão digital. Em seguida, vamos definir os recursos adicionais necessários para garantir a auto-inscrição do certificado.

### <a name="setting-up-prerequisite-resources"></a>Criação de recursos pré-requisitos
Como mencionado anteriormente, um certificado apresentado como um conjunto de escala de máquina virtual é recuperado do cofre pelo serviço Microsoft.Compute Resource Provider, utilizando a sua identidade de primeira parte e em nome do operador de implementação. Para a auto-tradução, isso mudará - mudaremos para usar uma identidade gerida, atribuída ao conjunto de escala de máquina virtual, e que é concedida permissão para os segredos do cofre.

Todos os excertos subsequentes devem ser implantados concomitantemente - são listados individualmente para análises e explicações play-by-play.

Primeiro, defina uma identidade atribuída ao utilizador (os valores padrão estão incluídos como exemplos) - consulte a [documentação oficial](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) para informações atualizadas:
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

Em seguida, conceda a esta identidade acesso aos segredos do cofre - consulte a [documentação oficial](/rest/api/keyvault/vaults/updateaccesspolicy) para informações atuais:
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
  - atribuir a identidade atribuída ao utilizador para o conjunto de escala de máquina virtual
  - declarar a dependência da escala de máquina virtual sobre a criação da identidade gerida, e sobre o resultado de conceder-lhe acesso ao cofre
  - declarar a extensão KeyVault VM, exigindo que recupere certificados observados no arranque[(documentação oficial)](../virtual-machines/extensions/key-vault-windows.md)
  - atualizar a definição da extensão VM do tecido de serviço para depender da extensão KVVM, e converter o cert do cluster em nome comum (estamos a fazer estas alterações num único passo, uma vez que se enquadram no âmbito do mesmo recurso.)

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
Note- se, embora não explicitamente listado acima, que removemos o URL do certificado de abóbada da secção 'OsProfile' do conjunto de escala de máquina virtual.
O último passo é atualizar a definição de cluster para alterar a declaração de certificado da impressão digital para o nome comum - aqui estamos também a fixar as impressões digitais do emitente:

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

Neste momento, pode executar as atualizações acima mencionadas numa única implantação; por seu lado, o serviço Service Fabric Resource Provider dividirá a atualização do cluster em várias etapas, conforme descrito no segmento de [conversão de certificados de cluster da impressão digital para nome comum.](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)

### <a name="analysis-and-observations"></a>Análise e observações
Esta secção é um ponto de vista para explicar as etapas acima detalhadas, bem como chamar a atenção para aspetos importantes.

#### <a name="certificate-provisioning-explained"></a>Provisionamento de certificados, explicado
A extensão KVVM, como agente de provisionamento, funciona continuamente numa frequência pré-determinada. Ao não conseguir obter um certificado observado, continuaria para o próximo na linha, e depois hibernaria até ao ciclo seguinte. A extensão SFVM, como agente de armadilhagem de botas de cluster, exigirá os certificados declarados antes que o cluster possa formar-se. Isto, por sua vez, significa que a extensão SFVM só pode ser executada após a recuperação bem sucedida dos certificados de cluster, denotados aqui pela ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` cláusula, e pela definição da extensão KeyVaultVM. ```json "requireInitialSync": true``` Isto indica à extensão KVVM que na primeira execução (após a implantação ou reinicialização) deve percorrer os certificados observados até que todos sejam descarregados com sucesso. Definir este parâmetro em falso, juntamente com a falha na recuperação dos certificados de cluster resultaria numa falha na implantação do cluster. Inversamente, exigir uma sincronização inicial com uma lista incorreda/inválida de certificados observados resultaria numa falha da extensão KVVM, pelo que, mais uma vez, uma falha na implantação do cluster.  

#### <a name="certificate-linking-explained"></a>Ligação de certificado, explicado
Deve ter reparado na bandeira 'linkOnRenewal' da extensão KVVM e no facto de estar definida como falsa. Estamos a abordar em profundidade o comportamento controlado por esta bandeira e as suas implicações no funcionamento de um aglomerado. Note que este comportamento é específico do Windows.

De acordo com a sua [definição:](../virtual-machines/extensions/key-vault-windows.md#extension-schema)
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Os certificados utilizados para estabelecer uma ligação TLS são normalmente [adquiridos como um cabo](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) através do Fornecedor de Suporte de Segurança do canal S – ou seja, o cliente não acede diretamente à chave privada do próprio certificado. [CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)Se esta propriedade estiver definida, o seu valor representa a impressão digital do certificado de "renovação", pelo que o canal S tentará, em vez disso, carregar o certificado ligado. Na verdade, vai atravessar esta lista ligada (e esperemos que aciclica) até que termine com o certificado 'final' – um sem marca de renovação. Esta funcionalidade, quando utilizada de forma criteriosa, é uma grande mitigação contra a perda de disponibilidade causada por certificados caducados (por exemplo). Noutros casos, pode ser a causa de interrupções que são difíceis de diagnosticar e mitigar. O canal S executa incondicionalmente a transversalização dos certificados sobre as suas propriedades de renovação - independentemente do assunto, emitentes ou quaisquer outros atributos específicos que participem na validação do certificado resultante pelo cliente. Com efeito, é possível que o certificado resultante não tenha uma chave privada associada, ou que a chave não tenha sido ACLed para o seu potencial consumidor. 
 
Se a ligação estiver ativa, a extensão KeyVault VM, ao recuperar um certificado observado do cofre, tentará encontrar certificados correspondentes e existentes para os ligar através da propriedade de extensão de renovação. A correspondência é (exclusivamente) baseada no Nome Alternativo do Sujeito (SAN), e funciona como exemplificada abaixo.
Assuma dois certificados existentes, da seguinte forma: A: CN = "Alice's accessories", SAN = {"alice.universalexports.com"}, renovação = '' B: CN = "Bob's bits", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, renovação = ''
 
Assuma que um certificado C é recuperado pelo kVVM ext: CN = "Malware de Mallory", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
A lista de SAN de A está totalmente incluída em C's, e assim A.renovação = C.thumbprint; A lista de SAN B tem uma intersecção comum com c's, mas não está totalmente incluída, por isso a renovação de B permanece vazia.
 
Qualquer tentativa de invocar a AcquireCredentialsHandle (canal S) neste estado no certificado A acabará por enviar C para a parte remota. No caso do Service Fabric, o [subsistema de Transporte](service-fabric-architecture.md#transport-subsystem) de um cluster utiliza o canal S para a autenticação mútua, pelo que o comportamento acima descrito afeta diretamente a comunicação fundamental do cluster. Continuando o exemplo acima, e assumindo que A é o certificado de cluster, o que acontece a seguir depende:
  - se a chave privada de C não for ACLd para a conta que a Fabric está a executar como, veremos falhas na aquisição da chave privada (SEC_E_UNKNOWN_CREDENTIALS ou similar)
  - se a chave privada de C estiver acessível, então veremos falhas de autorização devolvidas pelos outros nós (CertificateNotMatched, unauthorizeda, etc.) 
 
Em qualquer dos casos, o transporte falha e o cluster pode diminuir; os sintomas variam. Para piorar as coisas, a ligação depende da ordem de renovação – que é ditada pela ordem da lista de certificados observados da extensão KVVM, do calendário de renovação no cofre ou mesmo de erros transitórios que alteram a ordem de recuperação.

Para mitigar tais incidentes, recomendamos:
  - não misturar as SANs de diferentes certificados de cofre; cada certificado de abóbada deve servir um propósito distinto, e seu sujeito e SAN deve refletir que com especificidade
  - incluir o nome comum na lista SAN (como, literalmente, "CN= <subject common name> ")  
  - se não estiver seguro, desative a ligação na renovação dos certificados previstos com a extensão KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Porquê utilizar uma identidade gerida atribuída pelo utilizador? Quais são as implicações da sua utilização?
À medida que surgiu dos snippets json acima, é necessária uma sequência específica das operações/atualizações para garantir o sucesso da conversão e manter a disponibilidade do cluster. Especificamente, o recurso de escala de máquina virtual declara e utiliza a sua identidade para recuperar segredos numa única (do ponto de vista do utilizador). A extensão VM do tecido de serviço (que arranca o cluster) depende da conclusão da extensão KeyVault VM, que depende da recuperação bem sucedida dos certificados observados. A extensão KVVM utiliza a identidade do conjunto de escala de máquina virtual para aceder ao cofre, o que significa que a política de acesso no cofre já deve ter sido atualizada antes da colocação do conjunto de escala de máquina virtual. 

Para eliminar a criação de uma identidade gerida, ou atribuí-la a outro recurso, o operador de implantação deve ter a função necessária (ManagedIdentityOperator) na subscrição ou no grupo de recursos, além das funções necessárias para gerir os outros recursos referenciados no modelo. 

Do ponto de vista da segurança, lembre-se que a máquina virtual (conjunto de escala) é considerada uma fronteira de segurança no que diz respeito à sua identidade Azure. Isto significa que qualquer aplicação acolhida no VM poderia, em princípio, obter um símbolo de acesso que representasse o VM - os tokens de acesso à identidade geridos são obtidos a partir do ponto final iMDS não autenticado. Se considerar o VM um ambiente partilhado ou multi-inquilino, então talvez este método de recuperação de certificados de cluster não seja indicado. É, no entanto, o único mecanismo de provisionamento adequado para a cadeência automática dos certificados.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Resolução de problemas e perguntas frequentes

*P:* Como inscrever-se programáticamente num certificado gerido pelo KeyVault?
*R*: Descubra o nome do emitente a partir da documentação KeyVault e, em seguida, substitua-o no roteiro abaixo.  
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
*R*: Se a declaração de certificado especificar as impressões digitais do emitente e o emitente direto do certificado não estiver incluído na lista de emitentes fixados, o certificado será considerado inválido - independentemente de a sua raiz ser ou não fidedigna pelo cliente. Por conseguinte, é fundamental garantir que a lista de emitentes está atualizada/ atualizada. A introdução de um novo emitente é um evento raro, e deve ser amplamente publicitada antes de começar a emitir certificados. 

Em geral, um PKI publicará e manterá uma declaração de prática de certificação, de acordo com o IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Entre outras informações, incluirá todos os emitentes ativos. Recuperar esta lista programáticamente pode diferir de um PKI para outro.   

Para pKIs internos da Microsoft, consulte a documentação interna sobre os pontos finais/SDKs utilizados para recuperar os emitentes autorizados; é da responsabilidade do proprietário do cluster sondar esta lista periodicamente, e garantir que a sua definição de cluster inclui *todos os* emitentes esperados.

*P:* Várias PKIs são suportadas? 
*A:* Sim; não pode declarar múltiplas entradas de CN no manifesto de cluster com o mesmo valor, mas pode listar emitentes de vários PKIs correspondentes ao mesmo CN. Não é uma prática recomendada, e as práticas de transparência dos certificados podem impedir a emissão desses certificados. No entanto, como forma de migrar de um PKI para outro, este é um mecanismo aceitável.

*P:* E se o certificado de agrupamento atual não for emitido ou não tiver o sujeito pretendido? 
*A*: Obter um certificado com o sujeito pretendido e adicioná-lo à definição do cluster como secundário, por impressão digital. Uma vez concluída a atualização com sucesso, inicie outra atualização de configuração de cluster para converter a declaração do certificado em nome comum. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
