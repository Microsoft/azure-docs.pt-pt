---
title: Azure Key Vault na Austrália do Azure
description: Orientação sobre como configurar e usar Azure Key Vault para o gerenciamento de chaves nas regiões australianas para atender aos requisitos específicos da política, regulamentos e legislação do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602126"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault na Austrália do Azure

O armazenamento seguro de chaves de criptografia e o gerenciamento do ciclo de vida da chave de criptografia são elementos críticos dentro dos sistemas criptográficos.  O serviço que fornece esse recurso no Azure é o Azure Key Vault. Key Vault foi acessado IRAP Security e ACSC Certified for PROTECTED.  Este artigo descreve as principais considerações ao usar o Key Vault para cumprir os controles de segurança de informações (ASD) da [informação sobre](https://acsc.gov.au/infosec/ism/) a Directorate dos sinais australianos.

Azure Key Vault é um serviço de nuvem que protege as chaves de criptografia e os segredos. Como esses dados são confidenciais e críticos para os negócios, Key Vault permite o acesso seguro a cofres de chaves, permitindo apenas usuários e aplicativos autorizados. Há três artefatos principais gerenciados e controlados por Key Vault:

- chaves
- segredos
- certificados

Este artigo se concentrará no gerenciamento de chaves usando Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagrama 1 – Azure Key Vault*

## <a name="key-design-considerations"></a>Considerações de design-chave

### <a name="deployment-options"></a>Opções de implementação

Há duas opções para criar cofres de chaves do Azure. As duas opções usam a família Thales nShield de HSM (módulos de segurança de hardware), são validadas pelo FIPS (Federal Information Processing Standards) e são aprovadas para armazenar chaves em ambientes protegidos. As opções são:

- **Cofres protegidos por software:** FIPS 140-2 nível 1 validado. Chaves armazenadas em um HSM. As operações de criptografia e descriptografia são executadas em recursos de computação no Azure.
- **Cofres protegidos por HSM:** FIPS 140-2 nível 2 validado. Chaves armazenadas em um HSM. As operações de criptografia e descriptografia são executadas no HSM.

O Key Vault dá suporte às chaves Rivest-Shamir-adlema (RSA) e de criptografia de curva elíptica (ECC). O padrão é as chaves RSA de 2048 bits, mas há uma opção avançada para RSA 3072 bits, RSA 4096-bit e chaves ECC.  Todas as chaves atendem aos controles do ISM, mas as chaves de curva elíptica são preferenciais.

### <a name="resource-operations"></a>Operações de recurso

Há várias pessoas envolvidas na Azure Key Vault:

- **Key Vault administrador:** Gerencia o ciclo de vida do cofre
- **Administrador da chave:** Gerencia o ciclo de vida das chaves no cofre
- **Desenvolvedor/operador:** Integrar chaves do cofre em aplicativos e serviços
- **Auditor** Monitorar o uso e o acesso da chave
- **Aplicativos** Usar chaves para proteger informações

O Azure Key Vault é protegido com duas interfaces separadas:

- **Plano de gerenciamento:** Esse plano lida com o gerenciamento do cofre e protegido pelo RBAC.
- **Plano de dados:** Esse plano lida com o gerenciamento e o acesso aos artefatos no cofre.  Protegido usando a política de acesso Key Vault.

Conforme exigido pelo ISM, a autenticação adequada e o autorização são necessários antes de um chamador (um usuário ou um aplicativo) antes que eles possam obter acesso ao cofre de chaves por qualquer plano.

O RBAC do Azure tem uma função interna para Key Vault, [Key Vault colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor), para controlar o gerenciamento dos cofres de chaves. Recomenda-se a criação de funções personalizadas alinhadas a funções mais granulares para gerenciar seus cofres de chaves.

>[!WARNING]
>Quando o acesso a chaves é habilitado por meio da política de acesso de Key Vault, o usuário ou aplicativo tem esse acesso a todas as chaves no cofre de chaves (por exemplo, se um usuário tem o acesso ' excluir ', ele pode excluir todas as chaves).  Portanto, vários cofres de chaves devem ser implantados para alinhar-se com os limites/domínios de segurança.

### <a name="networking"></a>Redes

Você pode configurar Key Vault firewalls e redes virtuais para controlar o acesso ao plano de dados.  Você pode permitir o acesso a usuários ou aplicativos em redes especificadas negando acesso a usuários ou aplicativos em todas as outras redes. Os [serviços confiáveis](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services) são uma exceção a esse controle se "permitir serviços confiáveis" estiver habilitado.  O controle de rede virtual não se aplica ao plano de gerenciamento.

O acesso a cofres de chaves deve ser explicitamente restrito ao conjunto mínimo de redes que têm usuários ou aplicativos que exigem acesso às chaves.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault dá suporte a BYOK.  O BYOK permite que os usuários importem chaves de suas infraestruturas de chave existentes.  O Thales fornece um [conjunto de ferramentas australianos](https://www.microsoft.com/download/details.aspx?id=45345) para dar suporte à transferência segura e à importação de chaves de um HSM externo (por exemplo, chaves geradas com uma estação de trabalho offline) em Key Vault.

### <a name="key-vault-auditing-and-logging"></a>Key Vault auditoria e log

O ACSC exige que as entidades da Comunidade usem os serviços do Azure apropriados para realizar o monitoramento em tempo real e relatórios sobre suas cargas de trabalho do Azure.

O registro em log é habilitado habilitando a configuração de diagnóstico **_"AuditEvent"_** nos valores de chave.  Os eventos de auditoria serão registrados na conta de armazenamento especificada.  O período de **_"RetentionInDays"_** deve ser definido de acordo com a política de retenção de dados.  As [operações](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret) no plano de gerenciamento e no plano de dados são auditadas e registradas. A [solução Azure Key Vault no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault) pode ser usada para examinar Key Vault logs do AuditEvent.  Vários outros serviços do Azure podem ser usados para processar e distribuir Key Vault AuditEvents.

### <a name="key-rotation"></a>Rotação de chaves

O armazenamento de chaves no Key Vault forneceu um único ponto para manter as chaves fora dos aplicativos que permitem que as chaves sejam atualizadas sem afetar o comportamento dos aplicativos. O armazenamento de chaves no Azure Key Vault permite várias estratégias para dar suporte à rotação de chaves:

- Manualmente
- Programaticamente via APIs
- Scripts de automação (por exemplo, usando o PowerShell e a automação do Azure)

Essas opções permitem que as chaves sejam giradas periodicamente para atender aos requisitos de conformidade ou de uma base ad hoc se houver preocupações que as chaves possam ter sido comprometidas.

#### <a name="key-rotation-strategies"></a>Estratégias de rotação de chaves

É importante desenvolver uma estratégia apropriada de rotação de chaves para chaves que são armazenadas no keyvault.  Usar a chave errada fará com que as informações sejam descriptografadas incorretamente e as chaves de perda podem levar à perda completa de acesso às informações.  Exemplos de estratégias de rotação de chaves para diferentes cenários incluem:

- **Dados do em andamento:** as informações voláteis são transmitidas entre 2 partes.  Quando uma tecla é girada, ambas as partes devem ter um mecanismo para a recuperação síncrona das chaves atualizadas do cofre de chaves.
- **Dados como REST:** Uma parte armazena dados criptografados e descriptografa-os no futuro para uso.  Quando uma tecla for girada, os dados deverão ser descriptografados com a chave antiga e, em seguida, criptografados com a nova chave girada.  Há abordagens para minimizar o impacto do processo de descriptografia/criptografia usando chaves de criptografia de chave (consulte o exemplo).  A Microsoft gerencia a maioria do processo relacionado à rotação de chaves para o armazenamento do Azure (consulte...)
- **Chaves de acesso:** vários serviços do Azure têm chaves de acesso que podem ser armazenadas em Key Vault (por exemplo, CosmosDB).  Os serviços do Azure têm chaves de acesso primárias e secundárias.  É importante que as duas chaves não sejam giradas ao mesmo tempo.  Portanto, uma chave deve ser girada depois de um ponto e a operação de chave foi verificada e a segunda chave pode ser girada.

### <a name="high-availability"></a>Elevada disponibilidade

O ISM tem vários controles relacionados à continuidade dos negócios.
Azure Key Vault tem várias camadas de redundância com conteúdo replicado dentro da região e para a [região](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)secundária emparelhada.

Quando o cofre de chaves está em um estado de failover, ele está no modo somente leitura e retornará ao modo de leitura/gravação o serviço primário será restaurado.

O ISM tem vários controles relacionados ao backup.  É importante desenvolver e executar planos de backup/restauração apropriados para cofres e suas chaves.

## <a name="key-lifecycle"></a>Ciclo de vida da chave

### <a name="key-operations"></a>Operações de chave

Key Vault oferece suporte às seguintes operações em uma chave:

- **criada** Permite que um cliente crie uma chave no Key Vault. O valor da chave é gerado por Key Vault e armazenado e não é liberado para o cliente. Chaves assimétricas podem ser criadas em Key Vault.
- **importe** Permite que um cliente importe uma chave existente para Key Vault. Chaves assimétricas podem ser importadas para Key Vault usando vários métodos de empacotamento diferentes dentro de uma construção JWK.
- **cumulativo** Permite que um cliente com permissões suficientes modifique os metadados (atributos de chave) associados a uma chave armazenada anteriormente no Key Vault.
- **apagar** Permite que um cliente com permissões suficientes para excluir uma chave de Key Vault.
- **lista** Permite que um cliente liste todas as chaves em um determinado Key Vault.
- **listar versões:** Permite que um cliente liste todas as versões de uma determinada chave em determinado Key Vault.
- **Obter:** Permite que um cliente recupere as partes públicas de uma determinada chave em um Key Vault.
- **backup** Exporta uma chave em um formulário protegido.
- **recuperar** Importa uma chave de backup anterior.

Há um conjunto correspondente de permissões que pode ser concedido a usuários, entidades de serviço ou aplicativos usando entradas de controle de acesso Key Vault para permitir que eles executem operações de chave.

Key Vault tem um recurso de exclusão reversível para permitir a recuperação de cofres e chaves excluídas. Por padrão, a **_"exclusão reversível"_** não está habilitada, mas, uma vez habilitada, os objetos são mantidos por 90 dias (o período de retenção) enquanto aparecem para serem excluídos.  Uma permissão adicional de **_"limpeza"_** permitirá a exclusão permanente de chaves se a opção **_"limpar proteção"_** estiver desabilitada.

Criar ou importar uma chave existente cria uma nova versão da chave.

### <a name="cryptographic-operations"></a>Operações criptográficas

O Key Vault também dá suporte a operações de criptografia usando chaves:

- **assinar e verificar:** esta operação é "assinar hash" ou "verificar hash". Key Vault não dá suporte a hash de conteúdo como parte da criação da assinatura.
- **criptografia/encapsulamento de chave:** esta operação é usada para proteger outra chave.
- **criptografar e descriptografar:** a chave armazenada é usada para criptografar ou descriptografar um único bloco de dados

Há um conjunto correspondente de permissões que pode ser concedido a usuários, entidades de serviço ou aplicativos usando entradas de controle de acesso Key Vault para permitir que eles executem operações criptográficas.

Há três atributos de chave que podem ser definidos para controlar se uma chave está habilitada e utilizável de operações criptográficas:

- **habilitado**
- **NBF:** não antes de habilitado antes da data especificada
- **exp:** data de expiração

## <a name="storage-and-keys"></a>Armazenamento e chaves

As chaves gerenciadas pelo cliente são mais flexibilidade e permitem a avaliação e o gerenciamento das chaves a serem controladas. Eles também habilitam a auditoria das chaves de criptografia usadas para proteger os dados.
Há três aspectos para o armazenamento e as chaves armazenadas no Key Vault:

- Key Vault chaves da conta de armazenamento gerenciado
- Criptografia do Serviço de Armazenamento do Azure (SSE) para dados em repouso
- Managed disks e Azure Disk Encryption

O gerenciamento de chaves da conta de armazenamento do Azure da Key Vault é uma extensão para o serviço de chave Key Vault que dá suporte à sincronização e à regeneração (rotação) de chaves de conta de armazenamento.  [Integração do armazenamento do Azure com o Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) (versão prévia) é recomendado quando lançado, pois fornece segurança superior e facilidade de uso.
O SSE usa duas chaves para gerenciar a criptografia de dados em repouso:

- Chaves de criptografia de chave (KEK)
- Chaves de criptografia de dados (DEK)

Embora a Microsoft gerencie o DEKs, a SSE tem a opção de usar a KEKs gerenciada pelo cliente que pode ser armazenada em Key Vault. Isso permite a rotação de chaves em Azure Key Vault de acordo com as políticas de conformidade apropriadas. Quando as chaves são giradas, o armazenamento do Azure criptografa novamente a chave de criptografia da conta para essa conta de armazenamento. Isso não resulta na nova criptografia de todos os dados e não há nenhuma outra ação necessária.

A SSE é usada para discos gerenciados, mas não há suporte para chaves gerenciadas pelo cliente.  A criptografia de discos gerenciados pode ser feita usando Azure Disk Encryption com chaves KEK gerenciadas pelo cliente no Key Vault.

## <a name="next-steps"></a>Próximos Passos

Examine o artigo sobre [Federação de identidade](identity-federation.md)

Examine a documentação e os tutoriais adicionais de Azure Key Vault na [biblioteca de referência](reference-library.md)
