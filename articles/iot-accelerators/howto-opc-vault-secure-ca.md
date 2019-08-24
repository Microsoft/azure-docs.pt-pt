---
title: Como executar o serviço de gerenciamento de certificados do cofre OPC com segurança – Azure | Microsoft Docs
description: Descreve como executar o serviço de gerenciamento de certificados do cofre OPC com segurança no Azure e outras diretrizes de segurança a serem consideradas.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997668"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>Como executar o serviço de gerenciamento de certificados do cofre OPC com segurança

Este artigo explica como executar o serviço de gerenciamento de certificados do cofre do OPC com segurança no Azure e outras diretrizes de segurança a serem consideradas.

## <a name="roles"></a>Funções

### <a name="trusted-and-authorized-roles"></a>Funções confiáveis e autorizadas

O microserviço do cofre OPC está configurado para permitir que funções distintas acessem várias partes do serviço.

> [!IMPORTANT]
> Durante a implantação, o script adiciona apenas o usuário que executa o script de implantação como um usuário para todas as funções.
> Essa atribuição de função deve ser revisada para uma implantação de produção e reconfigurada adequadamente seguindo as diretrizes abaixo.
> Essa tarefa requer atribuição manual de funções e serviços no portal de aplicativos empresariais do Azure AD.

### <a name="certificate-management-service-roles"></a>Funções do serviço de gerenciamento de certificados

O microserviço do compartimento OPC define as seguintes funções:

- **Leitor**: Por padrão, qualquer usuário autenticado no locatário tem acesso de leitura. 
  - Acesso de leitura a aplicativos e solicitações de certificado. Pode listar e consultar aplicativos e solicitações de certificado. Além disso, as informações de descoberta de dispositivo e os certificados públicos estão acessíveis com acesso de leitura.
- **Gravador**: A função de gravador é atribuída a um usuário para adicionar permissões de gravação para determinadas tarefas. 
  - Acesso de leitura/gravação a aplicativos e solicitações de certificado. Pode registrar, atualizar e cancelar o registro de aplicativos. Pode criar solicitações de certificado e obter certificados e chaves privadas aprovadas. Também pode excluir chaves privadas.
- **Aprovador**: A função de aprovador é atribuída a um usuário para aprovar ou rejeitar solicitações de certificado. A função não inclui nenhuma outra função.
  - Além da função de aprovador para acessar a API de microserviço do compartimento OPC, o usuário também deve ter a permissão de assinatura de chave em Key Vault para poder assinar os certificados.
  - A função de gravador e aprovador deve ser atribuída a usuários diferentes.
  - A função principal do aprovador é a aprovação da geração e rejeição de solicitações de certificado.
- **Administrador**: A função de administrador é atribuída a um usuário para gerenciar os grupos de certificados. A função não oferece suporte à função de aprovador, mas inclui a função de gravador.
  - O administrador pode gerenciar os grupos de certificados, alterar a configuração e revogar certificados de aplicativo emitindo uma nova CRL.
  - Idealmente, as funções de autor, de aprovador e de administrador são atribuídas a diferentes usuários. Para segurança adicional, um usuário com a função de aprovador ou de administrador precisa também de permissão de assinatura de chave no keyvault para emitir certificados ou para renovar um certificado de autoridade de certificação do emissor.
  - Além da função de administração de microserviço, a função inclui também, mas não se limita a:
    - Responsável por administrar a implementação das práticas de segurança da autoridade de certificação.
    - Gerenciamento da geração, revogação e suspensão de certificados. 
    - Gerenciamento do ciclo de vida da chave de criptografia (por exemplo, a renovação das chaves da AC do emissor).
    - Instalação, configuração e manutenção de serviços que operam a autoridade de certificação.
    - A operação diária dos serviços. 
    - Backup e recuperação de CA e banco de dados.

### <a name="other-role-assignments"></a>Outras atribuições de função

As funções a seguir também devem ser consideradas e atribuídas ao executar o serviço:

- Proprietário de negócios do contrato de aquisição de certificado com a autoridade de certificação raiz externa (no caso de o proprietário comprar certificados de uma CA externa ou operar uma AC subordinada a uma AC externa).
- Desenvolvimento e validação da autoridade de certificação.
- Revisão de registros de auditoria.
- Equipe que ajuda a dar suporte à autoridade de certificação ou a gerenciar os recursos físicos e de nuvem, mas não são diretamente confiáveis para executar as operações de CA que estão definidas para estar na função autorizada. O conjunto de tarefas que as pessoas na função autorizada tem permissão para executar também devem ser documentados.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Associações de funções confiáveis e autorizadas devem ser revisadas anualmente

A associação de funções confiáveis e autorizadas deve ser revisada pelo menos trimestral para garantir que o conjunto de pessoas (para processos manuais) ou identidades de serviço (para processos automatizados) em cada função seja mantido no mínimo.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>O processo de emissão de certificado deve impor a separação de funções entre o solicitante de certificado e o aprovador

O processo de emissão de certificado deve impor a separação de funções entre o solicitante de certificado e as funções de aprovador de certificado (pessoas ou sistemas automatizados). A emissão de certificado deve ser autorizada por uma função de aprovador de certificado que verifica se o solicitante de certificado está autorizado a obter certificados. As pessoas que mantêm a função de aprovador de certificados devem ser uma pessoa autorizada formalmente.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>O gerenciamento de função com privilégios deve restringir o acesso e ser revisado trimestral

A atribuição de funções com privilégios, como autorizar a associação do grupo Administradores e aprovadores, deve ser restrita a um conjunto limitado de funcionários autorizados. Qualquer alteração de função privilegiada deve ter acesso revogado dentro de 24 horas. Por fim, as atribuições de função com privilégios devem ser revisadas trimestralmente e todas as atribuições desnecessárias ou expiradas devem ser removidas.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>As funções com privilégios devem usar a autenticação de dois fatores

A autenticação multifator (autenticação de dois fatores, MFA ou TFA) deve ser usada para logons interativos de aprovadores e administradores para o serviço.

## <a name="certificate-service-operation-guidelines"></a>Diretrizes de operação do serviço de certificado

### <a name="operational-contacts"></a>Contatos operacionais

O serviço de certificado deve ter um plano de resposta de segurança atualizado no arquivo, que contém contatos de resposta a incidentes operacionais detalhados.

### <a name="security-updates"></a>Atualizações de segurança

Todos os sistemas devem ser monitorados continuamente e atualizados com as últimas atualizações de segurança/conformidade com patches.

> [!IMPORTANT]
> O repositório GitHub do serviço de cofre do OPC é atualizado continuamente com patches de segurança. As atualizações no GitHub devem ser monitoradas e as atualizações são aplicadas ao serviço em intervalos regulares.

### <a name="security-monitoring"></a>Monitorização de segurança

Assine ou implemente o monitoramento de segurança apropriado, por exemplo, inscrevendo-se em uma solução de monitoramento central (por exemplo, central de segurança do Azure, solução de monitoramento do O365) e configure-a adequadamente para garantir que os eventos de segurança sejam transmitidos para o solução de monitoramento.

> [!IMPORTANT]
> Por padrão, o serviço de cofre do OPC é implantado com o [aplicativo Azure](https://docs.microsoft.com/azure/azure-monitor/app/devops) insights como uma solução de monitoramento. É altamente recomendável adicionar uma solução de segurança como a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-security-of-open-source-software-components"></a>Avaliar a segurança de componentes de software livre

Todos os componentes de software livre usados em um produto ou serviço devem ser livres de vulnerabilidades de segurança moderadas ou mais.

> [!IMPORTANT]
> O repositório GitHub do serviço de cofre do OPC está verificando todos os componentes durante as compilações de integração contínua para vulnerabilidades. As atualizações no GitHub devem ser monitoradas e as atualizações são aplicadas ao serviço em intervalos regulares.

### <a name="maintain-an-inventory"></a>Manter um inventário

Um inventário de ativos deve ser mantido para todos os hosts de produção (incluindo máquinas virtuais persistentes), dispositivos, todos os intervalos de endereços IP internos, VIPs e nomes de domínio DNS públicos. Esse inventário deve ser atualizado com adição ou remoção de um sistema, endereço IP do dispositivo, VIP ou domínio DNS público dentro de 30 dias.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventário da implantação de produção do microserviço do cofre OPC do Azure padrão: 

No **Azure**:
- **Plano do serviço de aplicativo**: Plano do serviço de aplicativo para hosts de serviço. Padrão S1.
- **Serviço de aplicativo** para o microserviço: O host do serviço de cofre do OPC.
- **Serviço** de aplicativo para aplicativo de exemplo: O host do aplicativo de exemplo do cofre OPC.
- **Padrão**de keyvault: Para armazenar segredos e chaves de Cosmos DB para os serviços Web.
- **Keyvault Premium**: Para hospedar as chaves de AC do emissor, para o serviço de assinatura, para a configuração do cofre e o armazenamento de chaves privadas do aplicativo.
- **Cosmos DB**: Banco de dados para solicitações de aplicativo e certificado. 
- **Application insights**: (opcional) monitoramento de solução para serviço Web e aplicativo.
- **Registro do aplicativo AzureAD**: Um registro para o aplicativo de exemplo, o serviço e o módulo de borda.

Para os serviços de nuvem, todos os nomes de host, grupos de recursos, nome de recurso, ID de assinatura, Tenantid usado para implantar o serviço devem ser documentados. 

No **IOT Edge** ou em um **servidor de IOT Edge**local:
- **Módulo de IOT Edge do cofre OPC**: Para dar suporte a um servidor de descoberta global de rede de fábrica OPC UA. 

Para os dispositivos IoT Edge, os nomes de host e os endereços IP devem ser documentados. 

### <a name="document-the-certification-authorities-cas"></a>Documentar as autoridades de certificação (CAs)

A documentação da hierarquia da autoridade de certificação deve conter todas as CAs operadas, incluindo todas as CAS subordinadas relacionadas, CAs pai e CAs raiz, mesmo quando elas não são gerenciadas pelo serviço. Um conjunto completo de todos os certificados de autoridade de certificação não expirados pode ser fornecido em vez da documentação formal.

> [!NOTE]
> O aplicativo de exemplo do cofre OPC dá suporte ao download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Documentar os certificados emitidos por todas as autoridades de certificação (CAs)

Um conjunto completo de todos os certificados emitidos nos últimos 12 meses deve ser fornecido para a documentação.

> [!NOTE]
> O aplicativo de exemplo do cofre OPC dá suporte ao download de todos os certificados usados e produzidos no serviço para documentação.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Documentar o SOP para excluir com segurança as chaves de criptografia

A exclusão de chave pode raramente ocorrer durante o tempo de vida de uma CA, por isso nenhum usuário tem o direito de exclusão de certificado do keyvault atribuído e por que não há APIs expostas para excluir um certificado de autoridade de certificação do emissor. O procedimento operacional padrão manual para excluir com segurança as chaves de criptografia da autoridade de certificação só está disponível acessando diretamente o keyvault no portal do Azure e excluindo o grupo de certificados no keyvault. Para garantir que a [](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) exclusão reversível de keyvault deve ser desabilitada imediatamente.

## <a name="certificates"></a>Certificados

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Os certificados devem estar em conformidade com o perfil de certificado mínimo

O serviço de cofre do OPC é uma CAs (autoridades de certificação) online que emite certificados de entidade final para assinantes.
O microserviço do compartimento OPC segue essas diretrizes na implementação padrão.

- Todos os certificados devem incluir os seguintes campos X. 509, conforme especificado abaixo:
  - O conteúdo do campo de versão deve ser v3. 
  - O conteúdo do campo serialNumber deve incluir pelo menos 8 bytes de entropia obtido de um gerador de número aleatório de FIPS (Federal Information Processing Standards) 140 aprovado.<br>
    > [!IMPORTANT]
    > O número de série do cofre OPC é, por padrão, 20 bytes e obtido do gerador de números aleatórios criptográficos do sistema operacional. O gerador de número aleatório é o FIPS 140 aprovado em dispositivos Windows, mas não em versões do Linux. Esse fato precisa ser considerado ao escolher uma implantação de serviço, que usa VMs Linux ou contêineres do Docker Linux, em que a tecnologia subjacente OpenSSL não é o FIPS 140 aprovado.
  - Os campos issuerUniqueID e subjectUniqueID não devem estar presentes.
  - Os certificados de entidade final devem ser identificados com a extensão de restrições básicas de acordo com a RFC 5280 da IETF.
  - O campo pathLenConstraint deve ser definido como 0 para o certificado de AC emissora. 
  - A extensão de uso estendido de chave deve estar presente e conter o conjunto mínimo de OIDs (identificadores de objeto de uso de chave estendida). O OID anyExtendedKeyUsage (2.5.29.37.0) não deve ser especificado. 
  - A extensão de CDP (ponto de distribuição de CRL) deve estar presente no certificado de autoridade de certificação do emissor.<br>
    > [!IMPORTANT]
    > A extensão de CDP (ponto de distribuição de CRL) está presente em certificados de AC do cofre OPC, mas os dispositivos OPC UA usam métodos personalizados para distribuir CRLs.
  - A extensão de acesso às informações da autoridade deve estar presente nos certificados do Assinante.<br>
    > [!IMPORTANT]
    > A extensão de acesso às informações da autoridade está presente nos certificados de assinante do cofre do OPC, no entanto, os dispositivos OPC UA usam métodos personalizados para distribuir informações de AC do emissor.
- Algoritmos assimétricos aprovados, comprimentos de chave, funções de hash e modos de preenchimento devem ser usados.
  - O **RSA** e o **SHA-2** são os únicos algoritmos com suporte (*).
  - A RSA pode ser usada para criptografia, troca de chaves e assinatura.
  - A criptografia RSA deve usar somente os modos de preenchimento OAEP, RSA-KEM ou RSA-PSS.
  - Comprimentos de chave > = 2048 bits são necessários.
  - Use a família SHA-2 de algoritmos de hash (SHA256, SHA384 e SHA512).
  - As chaves de AC raiz RSA com um tempo de vida típico > = 20 anos devem ser de 4096 bits ou mais.
  - As chaves de AC do emissor RSA devem ter pelo menos 2048 bits; se a data de expiração do certificado de autoridade de certificação for posterior a 2030, a chave da autoridade de certificação deverá ser de 4096 bits ou mais.
- Tempo de vida do certificado
  - Certificados de AC raiz: O período de validade máximo do certificado para autoridades de certificação raiz não deve exceder 25 anos.
  - Subautoridade de certificação ou certificados de AC do emissor online: O período de validade máximo do certificado para CAs que estão online e que emitem somente certificados do assinante não deve exceder seis anos. Para essas CAs, a chave de assinatura privada relacionada não deve ser usada por mais de três anos para emitir novos certificados.<br>
    > [!IMPORTANT]
    > O certificado do emissor como ele é gerado no microserviço do cofre OPC padrão sem AC raiz externa é tratado como uma subautoridade de certificação online com requisitos e vidas de vida correspondentes. O tempo de vida padrão é definido como cinco anos com um comprimento de chave > = 2048.
  - Todas as chaves assimétricas devem ter um tempo de vida máximo de cinco anos, tempo de vida de um ano recomendado.<br>
    > [!IMPORTANT]
    > Por padrão, os tempos de vida dos certificados de aplicativo emitidos com o cofre OPC têm um tempo de vida de dois anos e devem ser substituídos a cada ano. 
  - Sempre que um certificado é renovado, ele é renovado com uma nova chave.
- Extensões específicas do OPC UA em certificados de instância do aplicativo
  - A extensão subjectAltName inclui o URI do aplicativo e os nomes de host, que também podem incluir endereços FQDN, IPv4 e IPv6.
  - O KeyUsage inclui digitalSignature, nonrepúdio, keyEncipherment e dataEncipherment.
  - O extendedKeyUsage inclui serverAuth e/ou clientAuth.
  - O authorityKeyIdentifier é especificado em certificados assinados.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Chaves de CA (autoridade de certificação) e certificados devem atender aos requisitos mínimos

- **Chaves privadas**: As chaves **RSA** devem ter pelo menos 2048 bits; se a data de expiração do certificado de autoridade de certificação for posterior a 2030, a chave da autoridade de certificação deverá ser de 4096 bits ou mais.
- **Tempo de vida**: O período de validade máximo do certificado para CAs que estão online e que emitem somente certificados do assinante não deve exceder seis anos. Para essas CAs, a chave de assinatura privada relacionada não deve ser usada por mais de três anos para emitir novos certificados.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>As chaves de CA são protegidas usando módulos de segurança de hardware (HSM)

- O OpcVault usa o Azure keyvault Premium e as chaves são protegidas por HSM (módulos de segurança de hardware) do FIPS 140-2 nível 2. 

Os módulos de criptografia que o Key Vault usa, seja o HSM ou software, validados por FIPS (padrões de processamento de informações federais).<br>
As chaves criadas ou importadas como protegidas por HSM são processadas dentro de um HSM, validadas para o FIPS 140-2 nível 2.<br>
As chaves criadas ou importadas como protegidas por software são processadas dentro de módulos criptográficos validados para o FIPS 140-2 nível 1.

## <a name="operational-practices"></a>Práticas operacionais

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Documentar e manter as práticas de PKI operacional padrão para registro de certificado

Documente e mantenha os procedimentos operacionais padrão (SOPs) para saber como o CAs emite certificados, incluindo: 
- Como o assinante é identificado e autenticado 
- Como a solicitação de certificado é processada e validada (se aplicável, inclua também como a renovação de certificado e as solicitações de rechaveamento são processadas) 
- Como os certificados emitidos são distribuídos para os assinantes 

O SOP do microserviço do cofre OPC é descrito na [visão geral](overview-opc-vault-architecture.md) e no [como gerenciar](howto-opc-vault-manage.md) documentos. As práticas seguem a especificação de arquitetura unificada OPC, parte 12: Descoberta e serviços globais.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Documentar e manter as práticas de PKI operacional padrão para revogação de certificado

O processo de revogação de certificado é descrito na [visão geral](overview-opc-vault-architecture.md) e no [como gerenciar](howto-opc-vault-manage.md) documentos.
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>Cerimônia de geração de chave de CA (autoridade de certificação) de documento 

A geração de chave de autoridade de certificação do emissor no microserviço do cofre OPC é simplificada devido ao armazenamento seguro no Azure keyvault e descrito na documentação [como gerenciar](howto-opc-vault-manage.md) .

No entanto, quando uma autoridade de certificação raiz externa está sendo usada, uma cerimônia de geração de chave de CA (autoridade de certificação) deve atender aos seguintes requisitos:

A cerimônia de geração de chave de CA deve ser executada em um script documentado que inclui pelo menos os seguintes itens: 
1. Definição de funções e responsabilidades do participante
2. Aprovação para conduta da cerimônia de geração de chave de CA
3. Hardware criptográfico e materiais de ativação necessários para a cerimônia
4. Preparação de hardware (incluindo atualização de informações de ativo/configuração e aprovação)
5. Instalação do sistema operacional
6. Etapas específicas executadas durante a cerimônia de geração de chave de CA, como: 
7. Instalação e configuração de aplicativos de CA
8. Geração de chave de CA
9. Backup de chave de CA
10. Assinatura de certificado de autoridade de certificação
9. Importação de chaves assinadas no HSM protegido do serviço.
11. Desligamento do sistema da AC
12. Preparação de materiais para armazenamento


## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a gerenciar com segurança o cofre OPC, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Proteger dispositivos OPC UA com o cofre OPC](howto-opc-vault-secure.md)