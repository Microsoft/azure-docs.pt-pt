---
title: Como gerir o serviço de gestão de certificados OPC Vault de forma segura - Azure / Microsoft Docs
description: Descreve como executar o serviço de gestão de certificados OPC Vault de forma segura em Azure, e revê outras diretrizes de segurança a considerar.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 01213cd4d04783d0d877b4565493f06fa3718f8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399640"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Executar o serviço de gestão de certificados OPC Vault de forma segura

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como executar o serviço de gestão de certificados OPC Vault de forma segura em Azure, e revê outras diretrizes de segurança a considerar.

## <a name="roles"></a>Funções

### <a name="trusted-and-authorized-roles"></a>Funções confiáveis e autorizadas

O microserviço OPC Vault permite que funções distintas acedam a várias partes do serviço.

> [!IMPORTANT]
> Durante a implementação, o script apenas adiciona o utilizador que executa o script de implementação como um utilizador para todas as funções. Para uma implantação de produção, deve rever esta atribuição de funções e reconfigurar adequadamente seguindo as orientações abaixo. Esta tarefa requer atribuição manual de funções e serviços no portal de Aplicações empresariais Azure Ative (Azure AD).

### <a name="certificate-management-service-roles"></a>Funções de serviço de gestão de certificados

O microserviço OPC Vault define as seguintes funções:

- **Leitor:** Por predefinição, qualquer utilizador autenticado no arrendatário leu o acesso. 
  - Leia o acesso aos pedidos de pedidos e pedidos de certificados. Pode listar e consultar pedidos de pedidos e pedidos de certificados. Também informações de descoberta de dispositivos e certificados públicos são acessíveis com acesso lido.
- **Escritor**: O papel de Escritor é atribuído a um utilizador para adicionar permissões de escrita para determinadas tarefas. 
  - Ler/Escrever acesso a pedidos e pedidos de certificados. Pode registar, atualizar e não registar aplicações. Pode criar pedidos de certificados e obter chaves e certificados privados aprovados. Também pode apagar as chaves privadas.
- **Aprovação**: A função de Aprovação é atribuída a um utilizador para aprovar ou rejeitar pedidos de certificados. O papel não inclui qualquer outro papel.
  - Além da função Approver para aceder à API de microservice OPC Vault, o utilizador deve também ter a permissão de assinatura chave no Cofre da Chave Azure para poder assinar os certificados.
  - O papel de Escritor e Aprovador deve ser atribuído a diferentes utilizadores.
  - O papel principal do Aprovador é a aprovação da geração e rejeição dos pedidos de certificados.
- **Administrador**: A função de administrador é atribuída a um utilizador para gerir os grupos de certificados. O papel não apoia o papel de Aprovador, mas inclui o papel de Escritor.
  - O administrador pode gerir os grupos de certificados, alterar a configuração e revogar os certificados de pedido através da emissão de uma nova Lista de Revogação de Certificados (CRL).
  - Idealmente, as funções de Escritor, Aprovação e Administrador são atribuídas a diferentes utilizadores. Para uma segurança adicional, um utilizador com a função Approver ou Administrador também precisa de permissão de assinatura de chaves no Cofre-Chave, para emitir certificados ou para renovar um certificado emitente ca.
  - Além do papel de administração de microserviços, o papel inclui, mas não se limita a:
    - Responsabilidade pela gestão da implementação das práticas de segurança da AC.
    - Gestão da geração, revogação e suspensão de certificados. 
    - Gestão criptográfica do ciclo de vida (por exemplo, a renovação das teclas CA do Emitente).
    - Instalação, configuração e manutenção de serviços que operam a AC.
    - Funcionamento diário dos serviços. 
    - AC e base de dados de backup e recuperação.

### <a name="other-role-assignments"></a>Outras atribuições de papéis

Considere também as seguintes funções quando estiver a executar o serviço:

- Empresário proprietário do contrato de aquisição de certificados com a autoridade de certificação de raiz externa (por exemplo, quando o proprietário compra certificados de uma AC externa ou opera um CA que é subordinado a uma AC externa).
- Desenvolvimento e validação da Autoridade de Certificados.
- Revisão dos registos de auditoria.
- O pessoal que ajuda a apoiar a AC ou a gerir as instalações físicas e em nuvem, mas que não são diretamente confiáveis para executar operações de CA, estão no papel *autorizado.* O conjunto de tarefas que as pessoas na função autorizada devem desempenhar também deve ser documentado.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Rever membros de funções fidedignas e autorizadas trimestralmente

Reveja a adesão a funções fidedignas e autorizadas pelo menos trimestralmente. Certifique-se de que o conjunto de pessoas (para processos manuais) ou identidades de serviço (para processos automatizados) em cada função é mantido ao mínimo.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Separação de funções entre solicitador de certificados e aprovador

O processo de emissão de certificados deve impor a separação de funções entre as funções do solicitador de certificados e as funções de aprovação de certificados (pessoas ou sistemas automatizados). A emissão de certificados deve ser autorizada por uma função de aprovação de certificado que verifique que o solicitador de certificados está autorizado a obter certificados. As pessoas que detenham a função de aprovação do certificado devem ser uma pessoa formalmente autorizada.

### <a name="restrict-assignment-of-privileged-roles"></a>Restringir a atribuição de funções privilegiadas

Deve restringir a atribuição de funções privilegiadas, tais como a autorização de adesão do grupo administradores e aprovadores a um conjunto limitado de pessoal autorizado. Quaisquer alterações de funções privilegiadas devem ter acesso revogado dentro de 24 horas. Finalmente, reveja as atribuições de funções privilegiadas trimestralmente e remova quaisquer atribuições não precisa ou caducada.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Funções privilegiadas devem usar a autenticação de dois fatores

Utilize a autenticação multi-factor (também chamada de autenticação de dois fatores) para os sign-ins interativos de Approvers e Administradores para o serviço.

## <a name="certificate-service-operation-guidelines"></a>Diretrizes de operação de serviço de certificado

### <a name="operational-contacts"></a>Contactos operacionais

O serviço de certificados deve ter um plano de resposta de segurança atualizado em arquivo, que contém contactos de resposta a incidentes operacionais detalhados.

### <a name="security-updates"></a>Atualizações de segurança

Todos os sistemas devem ser monitorizados e atualizados continuamente com as mais recentes atualizações de segurança.

> [!IMPORTANT]
> O repositório GitHub do serviço OPC Vault é continuamente atualizado com patches de segurança. Monitorize estas atualizações e aplique-as ao serviço a intervalos regulares.

### <a name="security-monitoring"></a>Monitorização de segurança

Subscreva ou implemente um controlo de segurança adequado. Por exemplo, subscreva uma solução central de monitorização (como o Azure Security Center ou a solução de monitorização Microsoft 365) e configufique-a adequadamente para garantir que os eventos de segurança sejam transmitidos à solução de monitorização.

> [!IMPORTANT]
> Por predefinição, o serviço OPC Vault é implantado com [a Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) como solução de monitorização. A adição de uma solução de segurança como [o Azure Security Center](https://azure.microsoft.com/services/security-center/) é altamente recomendada.

### <a name="assess-the-security-of-open-source-software-components"></a>Avaliar a segurança dos componentes de software de código aberto

Todos os componentes de código aberto utilizados dentro de um produto ou serviço devem estar isentos de vulnerabilidades de segurança moderadas ou maiores.

> [!IMPORTANT]
> Durante as construções de integração contínua, o repositório GitHub do serviço OPC Vault analisa todos os componentes para vulnerabilidades. Monitorize estas atualizações no GitHub e aplique-as ao serviço a intervalos regulares.

### <a name="maintain-an-inventory"></a>Manter um inventário

Mantenha um inventário de ativos para todos os anfitriões de produção (incluindo máquinas virtuais persistentes), dispositivos, todas as gamas internas de endereços IP, VIPs e nomes de domínio DNS públicos. Sempre que adicionar ou remover um sistema, endereço IP do dispositivo, vip ou domínio PÚBLICO DNS, deve atualizar o inventário dentro de 30 dias.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventário da implantação padrão de produção de microserviços Azure OPC Vault 

Em Azure:
- **Plano de Serviço de Aplicações**: Plano de serviço de aplicações para anfitriões de serviço. S1 predefinido.
- **Serviço de aplicações** para microserviço: O anfitrião do serviço OPC Vault.
- **Serviço de aplicação** para aplicação de amostra: O anfitrião da aplicação da amostra OPC Vault.
- **Key Vault Standard**: Para armazenar segredos e chaves DB Azure Cosmos para os serviços web.
- **Key Vault Premium**: Para hospedar as teclas Emiter CA, para o serviço de assinatura e para a configuração do cofre e armazenamento de chaves privadas de aplicação.
- **Azure Cosmos DB**: Base de dados para pedidos de pedidos e pedidos de certificados. 
- **Aplicações Insights**: (opcional) Solução de monitorização para serviço web e aplicação.
- Registo de **pedido azure AD**: Registo para o pedido de amostra, o serviço e o módulo de borda.

Para os serviços na nuvem, todos os nomes de anfitriões, grupos de recursos, nomes de recursos, IDs de subscrição e IDs de inquilino utilizados para implantar o serviço devem ser documentados. 

No Azure IoT Edge ou num servidor local IoT Edge:
- **Módulo OPC Vault IoT Edge**: Para suportar uma rede de fábrica OPC UA Global Discovery Server. 

Para os dispositivos IoT Edge, os hostnames e endereços IP devem ser documentados. 

### <a name="document-the-certification-authorities-cas"></a>Documentar as Autoridades de Certificação (AC)

A documentação da hierarquia da CA deve conter todos os CAs operados. Isto inclui todos os CAs subordinados relacionados, CAs-mãe e CAs de raiz, mesmo quando não são geridos pelo serviço. Em vez de documentação formal, você pode fornecer um conjunto exaustivo de todos os certificados de CA não expirados.

> [!NOTE]
> A aplicação de amostra OPC Vault suporta o download de todos os certificados utilizados e produzidos no serviço para documentação.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentar os certificados emitidos por todas as Autoridades de Certificação (AA)

Forneça um conjunto exaustivo de todos os certificados emitidos nos últimos 12 meses.

> [!NOTE]
> A aplicação de amostra OPC Vault suporta o download de todos os certificados utilizados e produzidos no serviço para documentação.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Documentar o procedimento de operação padrão para eliminar de forma segura as chaves criptográficas

Durante a vida de um AC, a eliminação das chaves só pode acontecer raramente. É por isso que nenhum utilizador tem o Certificado de Cofre chave Eliminar o direito atribuído, e por que não existem APIs expostos para apagar um certificado de CA emitente. O procedimento de funcionamento padrão manual para eliminar de forma segura as chaves criptográficas da autoridade de certificação só está disponível acedendo diretamente ao Cofre-Chave no portal Azure. Também pode eliminar o grupo de certificados no Key Vault. Para garantir a eliminação imediata, desative a funcionalidade [de eliminação suave do Cofre de Chaves.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>Certificados

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Os certificados devem respeitar o perfil mínimo do certificado

O serviço OPC Vault é um CA online que emite certificados de entidade final para assinantes. O microserviço OPC Vault segue estas diretrizes na implementação padrão.

- Todos os certificados devem incluir os seguintes campos X.509, conforme especificado a seguir:
  - O conteúdo do campo de versão deve ser v3. 
  - O conteúdo do campo serialNumber deve incluir pelo menos 8 bytes de entropia obtidos a partir de um gerador de números aleatórios aprovado fips (Federal Information Processing Standards) 140.<br>
    > [!IMPORTANT]
    > O número de série do Cofre OPC é por predefinição de 20 bytes, e é obtido a partir do gerador de números aleatórios criptográficos do sistema operativo. O gerador de números aleatórios é o FIPS 140 aprovado em dispositivos Windows, mas não no Linux. Considere isto ao escolher uma implementação de serviço que utilize os recipientes Linux VMs ou Linux Docker, nos quais a tecnologia subjacente OpenSSL não é aprovada pelo FIPS 140.
  - Os campos emitenteUniqueID e SubjectUniqueID não devem estar presentes.
  - Os certificados de entidade final devem ser identificados com a extensão de restrições básicas, de acordo com o IETF RFC 5280.
  - O campo pathLenConstraint deve ser definido para 0 para o certificado de CA emissor. 
  - A extensão de utilização da chave estendida deve estar presente e deve conter o conjunto mínimo de identificadores de objetos de utilização de chaves estendidas (OIDs). Não deve ser especificado o OID de AnyExtendedKeyUsage (2.5.29.37.0). 
  - A extensão crl Distribution Point (CDP) deve estar presente no certificado EMITENTE CA.<br>
    > [!IMPORTANT]
    > A extensão cdp está presente nos certificados OPC Vault CA. No entanto, os dispositivos OPC UA utilizam métodos personalizados para distribuir CRLs.
  - A extensão de Acesso à Informação da Autoridade deve estar presente nos certificados de subscrição.<br>
    > [!IMPORTANT]
    > A extensão de Acesso à Informação da Autoridade está presente nos certificados de subscrição do OPC Vault. No entanto, os dispositivos OPC UA utilizam métodos personalizados para distribuir informações do Emitente CA.
- Devem ser utilizados algoritmos assimétricos aprovados, comprimentos de chave, funções de haxixe e modos de enchimento.
  - RSA e SHA-2 são os únicos algoritmos suportados.
  - A RSA pode ser usada para encriptação, troca de chaves e assinatura.
  - A encriptação RSA deve utilizar apenas os modos de enchimento OAEP, RSA-KEM ou RSA-PSS.
  - São necessários comprimentos de chave superiores ou iguais aos de 2048.
  - Utilize a família SHA-2 de algoritmos de haxixe (SHA256, SHA384 e SHA512).
  - As teclas RSA Root CA com uma vida útil típica superior ou igual a 20 anos devem ser de 4096 bits ou superiores.
  - As teclas RSA Emitente CA devem ser pelo menos 2048 bits. Se a data de validade do certificado de CA for depois de 2030, a chave CA deve ser de 4096 bits ou superior.
- Vida útil do certificado
  - Certificados root CA: O prazo máximo de validade do certificado para os CAs de raiz não deve exceder 25 anos.
  - Certificados de CA ou emitentes online: O prazo máximo de validade do certificado para CAs que estejam online e emita apenas certificados de subscrição não deve exceder 6 anos. Para estes CA, a chave de assinatura privada colidída não deve ser utilizada mais de 3 anos para emitir novos certificados.<br>
    > [!IMPORTANT]
    > O certificado Emitente, tal como é gerado no microservice OPC Vault padrão sem root ca externo, é tratado como um Sub CA on-line, com os respetivos requisitos e vidas. O tempo de vida padrão é definido para 5 anos, com um comprimento de chave maior ou igual a 2048.
  - Todas as chaves assimétricas devem ter uma vida máxima de 5 anos e uma vida de 1 ano recomendada.<br>
    > [!IMPORTANT]
    > Por padrão, as vidas dos certificados de candidatura emitidos com o Cofre OPC têm uma vida útil de 2 anos, e devem ser substituídas todos os anos. 
  - Sempre que um certificado é renovado, é renovado com uma nova chave.
- Extensões específicas da OPC UA em certificados de instância de aplicação
  - A extensão do nome de assuntoAltName inclui a aplicação Uri e hostnames. Estes também podem incluir endereços FQDN, IPv4 e IPv6.
  - A chaveUsage inclui digitalSignature, nonRepudiation, keyEncipherment e dataEncipherment.
  - O estendidoKeyUsage inclui o serverAuth e o clientAuth.
  - O autor da autoridade É especificado em certificados assinados.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>As chaves e certificados da AC devem satisfazer os requisitos mínimos

- **Chaves privadas**: As chaves RSA devem ser pelo menos 2048 bits. Se a data de validade do certificado de CA for depois de 2030, a chave CA deve ser de 4096 bits ou superior.
- **Vida útil**: O prazo máximo de validade do certificado para CAs que estejam online e emita apenas certificados de subscrição não deve exceder 6 anos. Para estes CA, a chave de assinatura privada colidída não deve ser utilizada mais de 3 anos para emitir novos certificados.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>As teclas CA são protegidas usando módulos de segurança de hardware

OpcVault utiliza Azure Key Vault Premium e as chaves estão protegidas por módulos de segurança de hardware de nível 140-2 (HSM). 

Os módulos criptográficos que o Key Vault utiliza, seja o HSM ou o software, são validados por FIPS. As chaves criadas ou importadas como protegidas pelo HSM são processadas dentro de um HSM, validado para FIPS 140-2 Nível 2. As chaves criadas ou importadas como protegidas por software são processadas dentro de módulos criptográficos validados para o FIPS 140-2 Nível 1.

## <a name="operational-practices"></a>Práticas operacionais

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentar e manter práticas padrão de PKI operacional para a inscrição de certificados

Documentar e manter os procedimentos operacionais padrão (SOPs) para a forma como os CAs emitem certificados, incluindo: 
- Como o assinante é identificado e autenticado. 
- Como o pedido de certificado é processado e validado (se aplicável, inclua também como a renovação de certificados e pedidos de rekey são processados). 
- Como os certificados emitidos são distribuídos aos assinantes. 

O microservice OPC Vault SOP é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [gere o serviço de certificados OPC Vault](howto-opc-vault-manage.md). As práticas seguem "OPC Unified Architecture Specification Part 12: Discovery and Global Services".


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentar e manter práticas padrão de PKI operacional para revogação de certificados

O processo de revogação do certificado é descrito na [arquitetura OPC Vault](overview-opc-vault-architecture.md) e [gere o serviço de certificados OPC Vault](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Cerimónia de geração chave do documento CA 

A geração de chaves Emiter CA no microserviço OPC Vault é simplificada, devido ao armazenamento seguro no Cofre da Chave Azure. Para obter mais informações, consulte [gerir o serviço de certificados OPC Vault](howto-opc-vault-manage.md).

No entanto, quando estiver a usar uma autoridade de certificação raiz externa, uma cerimónia de geração de chaves da AC deve respeitar os seguintes requisitos.

A cerimónia de geração-chave da CA deve ser realizada contra um script documentado que inclua pelo menos os seguintes itens: 
- Definição de papéis e responsabilidades dos participantes.
- Aprovação para a realização da cerimônia de geração chave da AC.
- Material criptográfico e materiais de ativação necessários para a cerimónia.
- Preparação de hardware (incluindo atualização de informação de ativo/configuração e desinsusação).
- Instalação do sistema operativo.
- Passos específicos realizados durante a cerimónia de geração chave da AC, tais como: 
  - Instalação e configuração de aplicações CA.
  - Geração chave CA.
  - Reserva de chave ca.
  - Assinatura de certificado de CA.
  - Importação de chaves assinadas no HSM protegido do serviço.
  - Desligado do sistema ca.
  - Preparação de materiais para armazenamento.


## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir de forma segura o Cofre OPC, pode:

> [!div class="nextstepaction"]
> [Dispositivos OPC UA seguros com cofre OPC](howto-opc-vault-secure.md)
