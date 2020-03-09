---
title: Como executar o serviço de gestão de certificados OPC Vault de forma segura - Azure [ Azure ] Microsoft Docs
description: Descreve como executar o serviço de gestão de certificados OPC Vault de forma segura em Azure, e revê outras diretrizes de segurança a considerar.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381127"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Executar o serviço de gestão de certificados OPC Vault de forma segura

Este artigo explica como executar o serviço de gestão de certificados OPC Vault de forma segura em Azure, e revê outras diretrizes de segurança a considerar.

## <a name="roles"></a>Papéis

### <a name="trusted-and-authorized-roles"></a>Funções confiáveis e autorizadas

O microserviço OPC Vault permite funções distintas para aceder a várias partes do serviço.

> [!IMPORTANT]
> Durante a implementação, o script apenas adiciona o utilizador que executa o script de implementação como utilizador para todas as funções. Para uma implantação de produção, deve rever esta atribuição de funções e reconfigurar adequadamente seguindo as orientações abaixo. Esta tarefa requer a atribuição manual de funções e serviços no portal de aplicações empresariais Azure Ative Directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Funções de serviço de gestão de certificados

O microserviço OPC Vault define as seguintes funções:

- **Leitor**: Por defeito, qualquer utilizador autenticado no inquilino leu o acesso. 
  - Leia o acesso a pedidos e pedidos de certificado. Pode listar e consultar pedidos de pedidos e pedidos de certificado. Também as informações sobre a descoberta do dispositivo e os certificados públicos são acessíveis com acesso à leitura.
- **Escritor**: O papel do Escritor é atribuído a um utilizador para adicionar permissões de escrita para determinadas tarefas. 
  - Ler/Escrever acesso a pedidos e pedidos de certificado. Pode registar, atualizar e desregistar as candidaturas. Pode criar pedidos de certificado e obter chaves e certificados privados aprovados. Também pode eliminar chaves privadas.
- **Aprovador**: A função de Aprovador é atribuída a um utilizador para aprovar ou rejeitar pedidos de certificado. O papel não inclui outro papel.
  - Além da função de Aprovador para aceder à API microserviço do Cofre OPC, o utilizador deve também ter a autorização de assinatura chave no Cofre de Chaves Azure para poder assinar os certificados.
  - O papel de Escritor e Aprovador deve ser atribuído a diferentes utilizadores.
  - O papel principal do Aprovador é a aprovação da geração e rejeição dos pedidos de certificados.
- **Administrador**: A função de Administrador é atribuída a um utilizador para gerir os grupos de certificados. O papel não apoia o papel de Aprovador, mas inclui o papel de Escritor.
  - O administrador pode gerir os grupos de certificados, alterar a configuração e revogar os certificados de candidatura através da emissão de uma nova Lista de Revogação de Certificados (CRL).
  - Idealmente, as funções de Escritor, Aprovador e Administrador são atribuídas a diferentes utilizadores. Para uma segurança adicional, um utilizador com a função de Aprovador ou Administrador também precisa de autorização de assinatura de chaves no Cofre chave, para emitir certificados ou renovar um certificado CA emitor.
  - Além do papel da administração de microserviços, o papel inclui, mas não se limita a:
    - Responsabilidade pela gestão da implementação das práticas de segurança da AC.
    - Gestão da geração, revogação e suspensão de certificados. 
    - Gestão criptográfica do ciclo de vida (por exemplo, a renovação das teclas CA emitentes).
    - Instalação, configuração e manutenção de serviços que operam a AC.
    - Operação diária dos serviços. 
    - Apoio e recuperação de bases de dados e ca e base de dados.

### <a name="other-role-assignments"></a>Outras atribuições de funções

Considere também as seguintes funções quando estiver a executar o serviço:

- Empresário do contrato de aquisição de certificados com a autoridade externa de certificação de raiz (por exemplo, quando o proprietário adquire certificados de uma CA externa ou opera uma AC subordinada a uma AC externa).
- Desenvolvimento e validação da Autoridade de Certificados.
- Revisão dos registos de auditoria.
- O pessoal que ajuda a apoiar a AC ou gerir as instalações físicas e em nuvem, mas não são diretamente confiáveis para realizar operações de AC, está no papel *autorizado.* O conjunto de tarefas que as pessoas no papel autorizado podem desempenhar também deve ser documentado.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Rever os membros de funções fidedignas e autorizadas trimestralmente

Reveja a adesão a funções fidedignas e autorizadas pelo menos trimestralmente. Certifique-se de que o conjunto de pessoas (para processos manuais) ou identidades de serviço (para processos automatizados) em cada função é reduzido ao mínimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separação de funções entre o solicitor de certificado e o aprovador

O processo de emissão de certificadodeve impor a separação de funções entre o solicitor de certificado e as funções de aprovador de certificados (pessoas ou sistemas automatizados). A emissão de certificados deve ser autorizada por uma função de aprovador de certificado que verifique se o solicitor de certificado está autorizado a obter certificados. As pessoas que detêm o papel de aprovador de certificado devem ser uma pessoa formalmente autorizada.

### <a name="restrict-assignment-of-privileged-roles"></a>Restringir a atribuição de funções privilegiadas

Deve restringir a atribuição de funções privilegiadas, tais como autorizar a adesão ao grupo administradores e aprovadores, a um conjunto limitado de pessoal autorizado. Quaisquer alterações de papéis privilegiadas devem ter acesso revogado dentro de 24 horas. Por último, reveja as atribuições de funções privilegiadas trimestralmente e remova quaisquer atribuições desnecessárias ou vencidas.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Funções privilegiadas devem utilizar a autenticação de dois fatores

Utilize a autenticação de vários fatores (também chamada de autenticação de dois fatores) para inscrições interativas de Aprovadores e Administradores para o serviço.

## <a name="certificate-service-operation-guidelines"></a>Diretrizes de funcionamento do serviço de certificados

### <a name="operational-contacts"></a>Contactos operacionais

O serviço de certificados deve ter um plano de resposta de segurança atualizado no ficheiro, que contém contactos de resposta a incidentes operacionais detalhados.

### <a name="security-updates"></a>Atualizações de segurança

Todos os sistemas devem ser monitorizados e atualizados com as mais recentes atualizações de segurança.

> [!IMPORTANT]
> O repositório GitHub do serviço OPC Vault é continuamente atualizado com patches de segurança. Monitorize estas atualizações e aplique-as ao serviço em intervalos regulares.

### <a name="security-monitoring"></a>Monitorização de segurança

Subscreva ou implemente uma monitorização de segurança adequada. Por exemplo, subscreva uma solução central de monitorização (como o Azure Security Center ou a solução de monitorização do Office 365) e configure-a adequadamente para garantir que os eventos de segurança sejam transmitidos à solução de monitorização.

> [!IMPORTANT]
> Por predefinição, o serviço OPC Vault é implantado com [o Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) como uma solução de monitorização. A adição de uma solução de segurança como o [Azure Security Center](https://azure.microsoft.com/services/security-center/) é altamente recomendada.

### <a name="assess-the-security-of-open-source-software-components"></a>Avaliar a segurança dos componentes de software de código aberto

Todos os componentes de código aberto utilizados dentro de um produto ou serviço devem estar isentos de vulnerabilidades de segurança moderadas ou maiores.

> [!IMPORTANT]
> Durante as construções contínuas de integração, o repositório GitHub do serviço OPC Vault digitaliza todos os componentes para vulnerabilidades. Monitorize estas atualizações no GitHub e aplique-as ao serviço em intervalos regulares.

### <a name="maintain-an-inventory"></a>Manter um inventário

Mantenha um inventário de ativos para todos os anfitriões de produção (incluindo máquinas virtuais persistentes), dispositivos, todas as gamas internas de endereços IP, VIPs e nomes públicos de domínio DNS. Sempre que adicionar ou remover um sistema, endereço IP do dispositivo, domínio VIP ou dNS público, deve atualizar o inventário dentro de 30 dias.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventário da implantação padrão de produção de microserviço saque do Cofre Azure OPC 

Em Azure:
- **Plano de Serviço de Aplicações**: Plano de serviço de aplicativos para anfitriões de serviços. Padrão S1.
- **Serviço de aplicações** para microserviços: O anfitrião do serviço OPC Vault.
- **Serviço de aplicações** para aplicação de amostras: O anfitrião da aplicação de amostras OPC Vault.
- **Key Vault Standard**: Para armazenar segredos e chaves Azure Cosmos DB para os serviços web.
- **Key Vault Premium**: Para acolher as teclas CA emitentes, para o serviço de assinatura, e para configuração do cofre e armazenamento de chaves privadas de aplicação.
- **Azure Cosmos DB**: Base de dados para pedidos de pedido e certificado. 
- **Informações sobre aplicações**: (opcional) Solução de monitorização para o serviço web e aplicação.
- **Registo de aplicação da AD Azure**: Uma inscrição para a aplicação da amostra, o serviço e o módulo de borda.

Para os serviços na nuvem, todos os nomes de anfitriões, grupos de recursos, nomes de recursos, iDs de subscrição e iDs de inquilinoutilizados para implementar o serviço devem ser documentados. 

Em Azure IoT Edge ou num servidor local IoT Edge:
- **Módulo OPC Vault IoT Edge**: Para suportar uma rede de fábrica OPC UA Global Discovery Server. 

Para os dispositivos IoT Edge, os nomes de anfitriões e os endereços IP devem ser documentados. 

### <a name="document-the-certification-authorities-cas"></a>Documentar as Autoridades de Certificação (AE)

A documentação da hierarquia da AC deve conter todos os CaAs operados. Isto inclui todos os CAs subordinados relacionados, cAs-mãe, e CAs raiz, mesmo quando não são geridos pelo serviço. Em vez de documentação formal, pode fornecer um conjunto exaustivo de todos os certificados ca não expirados.

> [!NOTE]
> A aplicação de amostra oPC Vault suporta o download de todos os certificados utilizados e produzidos no serviço para documentação.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentar os certificados emitidos por todas as autoridades de certificação (AE)

Forneça um conjunto exaustivo de todos os certificados emitidos nos últimos 12 meses.

> [!NOTE]
> A aplicação de amostra oPC Vault suporta o download de todos os certificados utilizados e produzidos no serviço para documentação.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documentar o procedimento normal de funcionamento para a apagar as chaves criptográficas de forma segura

Durante a vida de um CA, a eliminação da chave só pode acontecer raramente. É por isso que nenhum utilizador tem certificado de cofre de chave Apague o direito de exclusão e por que não existem APIs expostos para eliminar um certificado CA emitente. O procedimento de funcionamento padrão manual para a desabater as chaves criptográficas da autoridade de certificação apenas está disponível acedendo diretamente ao Key Vault no portal Azure. Também pode eliminar o grupo de certificados no Cofre chave. Para garantir a eliminação imediata, desative a funcionalidade de eliminação suave do [Cofre chave.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>certificados

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Os certificados devem cumprir o perfil mínimo do certificado

O serviço OPC Vault é um CA on-line que emite certificados de entidadefinal para assinantes. O microserviço OPC Vault segue estas orientações na implementação padrão.

- Todos os certificados devem incluir os seguintes campos X.509, conforme especificado abaixo:
  - O conteúdo do campo de versão deve ser v3. 
  - O conteúdo do campo serialNumber deve incluir pelo menos 8 bytes de entropia obtidos a partir de um FIPS (Normas Federais de Processamento de Informação) 140 gerador de números aleatórios aprovado.<br>
    > [!IMPORTANT]
    > O número de série do Cofre OPC é por padrão de 20 bytes, e é obtido a partir do gerador de números aleatórios criptográficos do sistema operativo. O gerador de números aleatórios é fips 140 aprovado em dispositivos Windows, mas não no Linux. Considere isto ao escolher uma implementação de serviço que utilize VMs Linux ou recipientes de estivador Linux, nos quais a tecnologia subjacente OpenSSL não é aprovada por FIPS 140.
  - Os campos UniqueID e sujeitos UniqueID não devem estar presentes.
  - Os certificados de entidade final devem ser identificados com a extensão de restrições básicas, de acordo com o IETF RFC 5280.
  - O campo pathLenConstraint deve ser definido para 0 para o certificado CA emissor. 
  - A extensão de utilização da chave estendida deve estar presente e deve conter o conjunto mínimo de identificadores de objetos de utilização de chaves estendidas (OIDs). O OID de utilização de teclas estendidas (2.5.29.37.0) não deve ser especificado. 
  - A extensão do Ponto de Distribuição CRL (CDP) deve estar presente no certificado CA emitente.<br>
    > [!IMPORTANT]
    > A extensão do CDP está presente nos certificados OPC Vault CA. No entanto, os dispositivos OPC UA utilizam métodos personalizados para distribuir CRLs.
  - A extensão de acesso à informação da Autoridade deve estar presente nos certificados de subscrição.<br>
    > [!IMPORTANT]
    > A extensão de Acesso de Informação da Autoridade está presente nos certificados de subscrição do OPC Vault. No entanto, os dispositivos OPC UA utilizam métodos personalizados para distribuir informações do Emitente CA.
- Devem ser utilizados algoritmos assimétricos aprovados, comprimentos-chave, funções de hash e modos de enchimento.
  - RSA e SHA-2 são os únicos algoritmos suportados.
  - A RSA pode ser usada para encriptação, troca de chaves e assinatura.
  - A encriptação RSA deve utilizar apenas os modos de enchimento OAEP, RSA-KEM ou RSA-PSS.
  - São necessários comprimentos-chave superiores ou iguais a 2048 bits.
  - Use a família SHA-2 de algoritmos de haxixe (SHA256, SHA384 e SHA512).
  - As teclas RSA Root CA com uma vida útil típica superior ou igual a 20 anos devem ser de 4096 bits ou superiores.
  - As teclas CA do Emitente RSA devem ser pelo menos 2048 bits. Se a data de validade do certificado CA for posterior a 2030, a chave CA deve ser de 4096 bits ou superior.
- Duração do certificado
  - Certificados de ca raiz: O período máximo de validade do certificado para os CaA radiculares não deve exceder 25 anos.
  - Certificados de Emissão Ca sub CA ou on-line: O período máximo de validade do certificado para os AG que estão on-line e emitir apenas certificados de subscrição não deve exceder 6 anos. Para estes AA, a chave de assinatura privada conexa não deve ser utilizada mais de 3 anos para emitir novos certificados.<br>
    > [!IMPORTANT]
    > O certificado Emitente, tal como é gerado no microserviço padrão do OPC Vault sem Root CA externo, é tratado como um Sub CA online, com os respetivos requisitos e vidas. O tempo de vida padrão é definido para 5 anos, com um comprimento chave maior ou igual a 2048.
  - Todas as chaves assimétricas devem ter uma duração máxima de 5 anos e uma vida útil recomendada de 1 ano.<br>
    > [!IMPORTANT]
    > Por padrão, as vidas dos certificados de candidatura emitidos com o OPC Vault têm uma vida útil de 2 anos, e devem ser substituídas todos os anos. 
  - Sempre que um certificado é renovado, é renovado com uma nova chave.
- Extensões específicas da OPC UA nos certificados de instância de aplicação
  - A extensão do sujeito AltName inclui a aplicação Uri e nomes de anfitriões. Estes também podem incluir endereços FQDN, IPv4 e IPv6.
  - A chaveUsage inclui digitalSignature, nonRepudiation, keyEncipherment e dataEncipherment.
  - O alargadoKeyUsage inclui serverAuth e clientAuth.
  - A autoridadeKeyIdentifier é especificada em certificados assinados.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Chaves e certificados ca devem satisfazer os requisitos mínimos

- **Chaves privadas**: As teclas RSA devem ser de, pelo menos, 2048 bits. Se a data de validade do certificado CA for posterior a 2030, a chave CA deve ser de 4096 bits ou superior.
- **Duração do tempo**de vida : O prazo máximo de validade do certificado para os AG que estejam online e emitir apenas certificados de assinante não deve exceder 6 anos. Para estes AA, a chave de assinatura privada conexa não deve ser utilizada mais de 3 anos para emitir novos certificados.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>As teclas CA estão protegidas usando módulos de segurança de hardware

OPcVault utiliza o Azure Key Vault Premium e as teclas estão protegidas por módulos de segurança de hardware de nível 2 (HSM) fips 140-2 Nível 2. 

Os módulos criptográficos que o Key Vault utiliza, seja HSM ou software, são validados por FIPS. As chaves criadas ou importadas como protegidas por HSM são processadas dentro de um HSM, validado para o Nível 2 do FIPS 140-2. As teclas criadas ou importadas como protegidas por software são processadas dentro de módulos criptográficos validados para o Nível 1 40-2 FIPS 1.

## <a name="operational-practices"></a>Práticas operacionais

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentar e manter práticas pki operacionais padrão para inscrição de certificados

Documentar e manter os procedimentos operacionais padrão (SOPs) para a forma como os CA emitem certificados, incluindo: 
- Como o assinante é identificado e autenticado. 
- Como o pedido de certificado é processado e validado (se aplicável, inclua também como a renovação do certificado e os pedidos de rechave são processados). 
- Como os certificados emitidos são distribuídos aos assinantes. 

O SOP microservice OPC Vault é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [Gere ncisa o serviço de certificado saque de oPC.](howto-opc-vault-manage.md) As práticas seguem "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentar e manter práticas pki operacionais padrão para revogação de certificados

O processo de revogação do certificado é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [gerencie o serviço de certificado saque de OPC.](howto-opc-vault-manage.md)
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimónia de geração chave do Documento CA 

A geração chave EMEmememCA no microserviço OPC Vault é simplificada, devido ao armazenamento seguro no Cofre chave Azure. Para mais informações, consulte Gerir o serviço de [certificado saque de OPC](howto-opc-vault-manage.md).

No entanto, quando se está a usar uma autoridade externa de certificação Root, uma cerimónia de geração chave ca deve aderir aos seguintes requisitos.

A cerimónia de geração-chave da AC deve ser realizada contra um roteiro documentado que inclua pelo menos os seguintes itens: 
- Definição de funções e responsabilidades dos participantes.
- Aprovação para a realização da cerimónia de geração chave da AC.
- Material criptográfico e de ativação necessários para a cerimónia.
- Preparação de hardware (incluindo atualização de informações sobre ativos/configuração e aprovação).
- Instalação do sistema operativo.
- Passos específicos realizados durante a cerimónia de geração chave ca, tais como: 
  - Instalação e configuração de aplicações CA.
  - Geração chave ca.
  - Reforço da chave CA.
  - Assinatura de certificado de ca.
  - Importação de chaves assinadas no HSM protegido do serviço.
  - Encerramento do sistema CA.
  - Preparação de materiais para armazenamento.


## <a name="next-steps"></a>Próximos passos

Agora que aprendeu a gerir com segurança o Cofre OPC, pode:

> [!div class="nextstepaction"]
> [Dispositivos OPC UA seguros com cofre OPC](howto-opc-vault-secure.md)
