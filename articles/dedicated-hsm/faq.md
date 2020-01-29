---
title: Perguntas frequentes – HSM dedicado do Azure | Microsoft Docs
description: Perguntas frequentes que abordam tópicos diferentes sobre o HSM dedicado do Azure
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: mbaldwin
ms.openlocfilehash: 338977c236ebb8f1b800eb6c28747a010e708300
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244220"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

Encontre respostas para perguntas comuns sobre Microsoft Azure HSM dedicado.

## <a name="the-basics"></a>Noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: o que é um HSM (módulo de segurança de hardware)?

Um HSM (módulo de segurança de hardware) é um dispositivo de computação física usado para proteger e gerenciar chaves de criptografia. As chaves armazenadas em HSMs podem ser usadas para operações criptográficas. O material da chave permanece com segurança em módulos de hardware resistentes a adulterações e evidentes. O HSM permite apenas que aplicativos autenticados e autorizados usem as chaves. O material das chaves nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: Qual é a oferta HSM dedicada do Azure?

O HSM dedicado do Azure é um serviço baseado em nuvem que fornece HSMs hospedados em data centers do Azure que estão diretamente conectados à rede virtual de um cliente. Esses HSMs são dispositivos de rede dedicados (Gemalto SafeNet Network HSM 7 Model A790). Eles são implantados diretamente no espaço de endereço IP privado de um cliente e a Microsoft não tem nenhum acesso à funcionalidade criptográfica dos HSMs. Somente o cliente tem controle total administrativo e criptográfico sobre esses dispositivos. Os clientes são responsáveis pelo gerenciamento do dispositivo e podem obter logs de atividade completos diretamente de seus dispositivos. Os HSMs dedicados ajudam os clientes a atender aos requisitos de conformidade/regulamentação, como FIPS 140-2 nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: qual hardware é usado para HSM dedicado?

A Microsoft estabeleceu uma parceria com a Gemalto para fornecer o serviço HSM dedicado do Azure. O dispositivo específico usado é o [modelo A790 de rede SafeNet Luna do HSM 7](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Esse dispositivo não apenas fornece um firmware validado de nível 3 de FIPS 140-2, mas também oferece baixa latência, alto desempenho e alta capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P: o que é um HSM usado?

Os HSMs são utilizados para armazenar chaves criptográficas que, por sua vez, são utilizadas para proporcionar funcionalidades criptográficas, como SSL (Secure Sockets Layer), encriptação de dados, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e assinar documentos.

### <a name="q-how-does-dedicated-hsm-work"></a>P: como funciona o HSM dedicado?

Os clientes podem provisionar HSMs em regiões específicas usando o PowerShell ou a interface de linha de comando. O cliente especifica a qual rede virtual os HSMs serão conectados e, uma vez provisionado, os HSMs estarão disponíveis na sub-rede designada em endereços IP atribuídos no espaço de endereço IP privado do cliente. Em seguida, os clientes podem se conectar aos HSMs usando SSH para gerenciamento e administração de dispositivos, configurar conexões de cliente HSM, inicializar HSMs, criar partições, definir e atribuir funções como o diretor de partição, o diretor de criptografia e o usuário de criptografia. Em seguida, o cliente usará as ferramentas de cliente HSM/SDK/software do Gemalto para executar operações criptográficas de seus aplicativos.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Qual software é fornecido com o serviço HSM dedicado?

O Gemalto fornece todo o software para o dispositivo HSM depois de provisionado pela Microsoft. O software está disponível no [portal de atendimento ao cliente do Gemalto](https://supportportal.gemalto.com/csm/). Os clientes que usam o serviço HSM dedicado devem ser registrados para o suporte do Gemalto e ter uma ID de cliente que habilite o acesso e o download do software relevante. O software cliente com suporte é a versão 7,2, que é compatível com a versão de firmware validada pelo FIPS 140-2 nível 3 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: o HSM dedicado do Azure oferece autenticação baseada em senha e em PED?

Neste momento, o HSM dedicado do Azure fornece apenas HSMs com autenticação baseada em senha.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: o HSM dedicado do Azure vai hospedar meus HSMs para mim?

A Microsoft oferece apenas o Gemalto SafeNet Luna Network HSM por meio do serviço HSM dedicado e não pode hospedar nenhum dispositivo fornecido pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: os recursos de pagamento (PIN/TEF) de suporte do HSM dedicado do Azure?

O serviço HSM dedicado do Azure usa dispositivos SafeNet Luna Network HSM 7 (Model A790). Esses dispositivos não dão suporte à funcionalidade específica do HSM de pagamento (como PIN ou TEF) ou certificações. Se você quiser que o serviço HSM dedicado do Azure ofereça suporte a HSMs de pagamento no futuro, passe os comentários para seu representante de conta da Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: em quais regiões do Azure o HSM dedicado está disponível?

Desde o final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões são planejadas e podem ser discutidas por meio de seu representante de conta da Microsoft.

* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A. Centro-Sul
* Sudeste Asiático
* Ásia Oriental
* Europa do Norte
* Europa Ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Canada Este
* Leste da Austrália
* Sudeste da Austrália

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: como meu aplicativo se conecta a um HSM dedicado?

Você usa Gemalto ferramentas de cliente HSM/SDK/software para executar operações criptográficas de seus aplicativos. O software está disponível no [portal de atendimento ao cliente do Gemalto](https://supportportal.gemalto.com/csm/). Os clientes que usam o serviço HSM dedicado devem ser registrados para o suporte do Gemalto e ter uma ID de cliente que habilite o acesso e o download do software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: um aplicativo pode se conectar ao HSM dedicado de uma VNET diferente em ou entre regiões?

Sim, você precisará usar o [emparelhamento VNET](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade entre redes virtuais. Para conectividade entre regiões, você deve usar o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: posso sincronizar o HSM dedicado com HSMs locais?

Sim, você pode sincronizar HSMs locais com HSM dedicado. A [VPN ponto a ponto ou a conectividade ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usada para estabelecer conectividade com sua rede local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: posso criptografar dados usados por outros serviços do Azure usando chaves armazenadas em HSM dedicado?

Não. Os HSMs dedicados do Azure só podem ser acessados de dentro de sua rede virtual.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves de um HSM local existente para o HSM dedicado?

Sim, se você tem HSMs Gemalto SafeNet no local. Há vários métodos. Consulte a documentação do Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: quais sistemas operacionais são compatíveis com o software cliente HSM dedicado?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, HyperV, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como fazer configurar meu aplicativo cliente para criar uma configuração de alta disponibilidade com várias partições de vários HSMs?

Para ter alta disponibilidade, você precisa definir a configuração do aplicativo cliente HSM para usar partições de cada HSM. Consulte a documentação do software cliente do Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: quais mecanismos de autenticação têm suporte pelo HSM dedicado?

O HSM dedicado do Azure usa dispositivos SafeNet de rede HSM 7 (modelo A790) e eles oferecem suporte à autenticação baseada em senha.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: quais SDKs, APIs e software cliente estão disponíveis para uso com o HSM dedicado?

PKCS # 11, Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar chaves de r $5/6 HSMs para HSMs dedicados do Azure?

Sim. Consulte o guia de migração do Gemalto. 

## <a name="using-your-hsm"></a>Usando o HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como fazer decidir se deseja usar o Azure Key Vault ou o HSM dedicado do Azure?

O HSM dedicado do Azure é a opção apropriada para empresas que estão migrando para aplicativos locais do Azure que usam HSMs. HSMs dedicados apresentam uma opção para migrar um aplicativo com alterações mínimas. Se as operações criptográficas forem executadas no código do aplicativo em execução em uma VM ou aplicativo Web do Azure, eles poderão usar o HSM dedicado. Em geral, os softwares encapsulados em execução em modelos IaaS (infraestrutura como serviço), que dão suporte a HSMs como um repositório de chaves, podem usar o HSM dedicado, como o gateway de aplicativo ou o Gerenciador de tráfego para SSL, ADCS (serviços de certificados Active Directory) ou ferramentas de PKI semelhantes, ferramentas/aplicativos usados para assinatura de documentos, assinatura de código ou um SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com chave mestra em um HSM usando um provedor EKM (gerenciamento extensível de chaves). A Azure Key Vault é adequada para aplicativos "transmitidos para a nuvem" ou para cenários de criptografia em repouso em que os dados do cliente são processados por cenários de PaaS (plataforma como serviço) ou SaaS (software como serviço), como a chave do cliente do Office 365, a proteção de informações do Azure , Azure Disk Encryption, Azure Data Lake Store criptografia com chave gerenciada pelo cliente, criptografia de armazenamento do Azure com chave gerenciada pelo cliente e SQL do Azure com chave gerenciada pelo cliente.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: quais cenários de uso melhor se adaptam ao HSM dedicado do Azure?

O HSM dedicado do Azure é mais adequado para cenários de migração. Isso significa que, se você estiver migrando aplicativos locais para o Azure que já estão usando HSMs. Isso fornece uma opção baixa-fricção para migrar para o Azure com alterações mínimas no aplicativo. Se as operações criptográficas forem executadas no código do aplicativo em execução na VM do Azure ou no aplicativo Web, o HSM dedicado poderá ser usado. Em geral, os softwares encapsulados em execução em modelos IaaS (infraestrutura como serviço), que dão suporte a HSMs como um repositório de chaves, podem usar o HSM dedicado, como:

* Gateway de aplicativo ou Gerenciador de tráfego para SSL sem criptografia
* ADCS (serviços de certificados Active Directory)
* Ferramentas de PKI semelhantes
* Ferramentas/aplicativos usados para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS) configurado com TDE (criptografia de banco de dados transparente) com a chave mestra em um HSM usando um provedor EKM (gerenciamento extensível de chaves)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: o HSM dedicado pode ser usado com a chave do cliente do Office 365, proteção de informações do Azure, Azure Data Lake Store, criptografia de disco, criptografia de armazenamento do Azure, Azure SQL TDE?

Não. O HSM dedicado é provisionado diretamente no espaço de endereço IP privado de um cliente para que ele não possa ser acessado por outros serviços do Azure ou da Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controle

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P: o cliente obtém total controle exclusivo sobre os HSMs com HSMs dedicados?

Sim. Cada dispositivo HSM é totalmente dedicado a um único cliente e ninguém mais tem controle administrativo depois de provisionado e a senha do administrador mudou.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: que nível de acesso a Microsoft tem ao meu HSM?

A Microsoft não tem nenhum controle administrativo ou criptográfico sobre o HSM. A Microsoft tem acesso de nível de monitor por meio da conexão de porta serial para recuperar a telemetria básica, como integridade de componente e temperatura. Isso permite que a Microsoft forneça notificação proativa de problemas de integridade. Se necessário, o cliente pode desabilitar essa conta.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P: Qual é a conta "tenantadmin" que a Microsoft usa, estou acostumado com o usuário administrador "admin" em SafeNet HSMs?

O dispositivo HSM é fornecido com um usuário padrão administrador com sua senha padrão usual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo estiver em um pool aguardando para ser provisionado pelos clientes. Isso não atenderia aos nossos requisitos de segurança estritos. Por esse motivo, definimos uma senha forte que é descartada no momento do provisionamento. Além disso, no tempo de provisionamento, criamos um novo usuário na função de administrador chamado "tenantadmin". Esse usuário tem a senha padrão e os clientes alteram isso como a primeira ação ao fazer logon primeiro no dispositivo provisionado recentemente. Esse processo garante altos níveis de segurança e mantém nossa promessa de controle administrativo exclusivo para nossos clientes. Deve-se observar que o usuário "tenantadmin" pode ser usado para redefinir a senha de usuário do administrador se um cliente preferir usar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: a Microsoft ou qualquer pessoa em chaves do Microsoft Access em meu HSM dedicado?

Não. A Microsoft não tem nenhum acesso às chaves armazenadas no HSM dedicado alocado pelo cliente.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: posso atualizar software/firmware em HSMs alocados para mim?

Para obter o melhor suporte, a Microsoft recomenda enfaticamente não atualizar o software/firmware no HSM. No entanto, o cliente tem controle administrativo total, incluindo a atualização de software/firmware se forem necessários recursos específicos de versões de firmware diferentes. Antes de fazer alterações, as implicações devem ser entendidas, por exemplo, o efeito do status validado por FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como fazer gerenciar HSM dedicado?

Você pode gerenciar HSMs dedicados acessando-os usando SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como fazer gerenciar partições no HSM dedicado?

O software cliente Gemalto HSM é usado para gerenciar os HSMs e as partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como fazer monitorar meu HSM?

Um cliente tem acesso completo aos logs de atividade do HSM por meio do syslog e do SNMP. Um cliente precisará configurar um servidor de syslog ou servidor SNMP para receber os logs ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: posso obter o log de acesso completo de todas as operações de HSM do HSM dedicado?

Sim. Você pode enviar logs do dispositivo HSM para um servidor syslog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: é possível configurar a alta disponibilidade na mesma região ou em várias regiões?

Sim. A configuração de alta disponibilidade e a configuração são executadas no software cliente HSM fornecido pelo Gemalto. Os HSMs da mesma rede virtual ou outros VNETs na mesma região ou entre regiões, ou HSMs locais conectados a uma VNET usando VPN site a site ou ponto a ponto podem ser adicionados à mesma configuração de alta disponibilidade. Deve-se observar que isso sincroniza somente o material da chave e não itens de configuração específicos, como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: posso adicionar HSMs de minha rede local a um grupo de alta disponibilidade com o HSM dedicado do Azure?

Sim. Eles devem atender aos requisitos de alta disponibilidade do SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: posso adicionar HSMs de Luna 5/6 de redes locais a um grupo de alta disponibilidade com o HSM dedicado do Azure?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade de um único aplicativo?

16 membros de um grupo de HA têm testes de aceleração total, com ótimos resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: Qual é o SLA para o serviço HSM dedicado?

Não há garantia de tempo de atividade específica fornecida para o serviço HSM dedicado. A Microsoft garantirá o acesso no nível da rede ao dispositivo e, portanto, os SLAs de rede do Azure padrão serão aplicados.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: como os HSMs usados no HSM dedicado do Azure são protegidos?

Os data centers do Azure têm amplos controles de segurança físicos e de procedimento. Além disso, os HSMs dedicados são hospedados em uma área de acesso restrito adicional do datacenter. Essas áreas têm controles de acesso físico adicionais e vigilância por câmera de vídeo para aumentar a segurança.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: o que acontece se houver um evento de violação de segurança ou violação de hardware?

O serviço HSM dedicado usa dispositivos SafeNet de rede HSM 7. Esses dispositivos dão suporte à detecção de adulteração física e lógica. Se houver algum evento de violação, os HSMs serão automaticamente zerados.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como fazer garantir que as chaves em meus HSMs dedicados não sejam perdidas devido a erros ou a um ataque interno mal-intencionado?

É altamente recomendável usar um dispositivo de backup HSM local para executar backup periódico regular dos HSMs para recuperação de desastre. Você precisará usar uma conexão VPN ponto a ponto ou site a site para uma estação de trabalho local conectada a um dispositivo de backup HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como fazer obter suporte para HSM dedicado?

O suporte é fornecido pela Microsoft e pelo Gemalto.  Se você tiver um problema com o acesso ao hardware ou à rede, gere uma solicitação de suporte com a Microsoft e, se tiver um problema com a configuração do HSM, o software e o desenvolvimento de aplicativos, gere uma solicitação de suporte com o Gemalto. Se você tiver um problema indeterminado, gere uma solicitação de suporte com a Microsoft e, em seguida, Gemalto poderá ser envolvido conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Como fazer obter o software cliente, a documentação e o acesso às diretrizes de integração do SafeNet Luna 7 HSM?

Após o registro para o serviço, será fornecida uma ID de cliente Gemalto que permite o registro no portal de suporte ao cliente do Gemalto. Isso permitirá o acesso a todos os softwares e documentações, bem como a habilitação de solicitações de suporte diretamente com o Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: se houver uma vulnerabilidade de segurança encontrada e um patch for lançado pelo Gemalto, que é responsável por atualizar/aplicar patches no sistema operacional/firmware?

A Microsoft não tem a capacidade de se conectar a HSMs alocados aos clientes. Os clientes devem atualizar e aplicar patches em seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: e se eu precisar reinicializar meu HSM?

O HSM tem uma opção de reinicialização de linha de comando. no entanto, estamos enfrentando problemas de reinicialização intermitente e, por esse motivo, é recomendado para a reinicialização mais segura que você gera uma solicitação de suporte com a Microsoft para que o dispositivo tenha reinicializado fisicamente. 

## <a name="cryptography-and-standards"></a>Criptografia e padrões

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: é seguro armazenar chaves de criptografia para meus dados mais importantes no HSM dedicado?

Sim, o HSM dedicado provisiona dispositivos SafeNet de rede HSM 7 que usam HSMs validados pelo FIPS 140-2 nível 3. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: Quais algoritmos e chaves de criptografia são compatíveis com o HSM dedicado?

O serviço HSM dedicado provisiona dispositivos SafeNet de rede HSM 7. Eles dão suporte a uma ampla variedade de algoritmos e tipos de chave de criptografia, incluindo: suporte completo a Suite B

* Assimétrica
  * RSA
  * DSA
  * Diffie-Hellman
  * Curva elíptica
  * Criptografia (ECDSA, ECDH, Ed25519, ECIES) com curvas nomeadas, definidas pelo usuário e brainpool, KCDSA
* Criadas
  * AES-GCM
  * DES triplo
  * DES
  * ARIA, SEMENTE
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Resumo de mensagem/HMAC: SHA-1, SHA-2, SM3
  * Derivação de chave: modo de contador SP800-108
  * Quebra automática de chave: SP800-38F
  * Geração de número aleatório: DRBG aprovado pelo FIPS 140-2 (modo SP 800-90 CTR), conformidade com BSI DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: o HSM FIPS 140-2 nível 3 é validado?

Sim. O serviço HSM dedicado provisiona dispositivos SafeNet de rede HSM 7 que usam HSMs validados pelo FIPS 140-2 nível 3.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: o que preciso fazer para ter certeza de que opere o HSM dedicado no modo validado do FIPS 140-2 nível 3?

O serviço HSM dedicado provisiona os dispositivos SafeNet Luna de rede HSM 7. Esses dispositivos usam HSMs validados pelo FIPS 140-2 nível 3. A configuração padrão implantada, o sistema operacional e o firmware também são validados por FIPS. Você não precisa realizar nenhuma ação para conformidade com o FIPS 140-2 nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: como um cliente garante que quando um HSM for desprovisionado, todo o material da chave será apagado?

Antes de solicitar o desprovisionamento, um cliente deve ter o HSM zerado usando as ferramentas de cliente HSM fornecidas pelo Gemalto.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: quantas operações criptográficas têm suporte por segundo com HSM dedicado?

O HSM dedicado provisiona dispositivos SafeNet de rede HSM 7 (modelo A790). Aqui está um resumo do desempenho máximo para algumas operações: 

* RSA-2048:10.000 transações por segundo
* P256 ECC: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: quantas partições podem ser criadas no HSM dedicado?

O modelo A790 SafeNet Luna HSM 7 usado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 partições e a adição de partições até esse limite incorrerá em custos de licenciamento extras e exigirá a instalação de um novo arquivo de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: quantas chaves podem ser suportadas no HSM dedicado?

O número máximo de chaves é uma função da memória disponível. O modelo SafeNet Luna 7 A790 em uso tem 32 MB de memória. Os números a seguir também se aplicam a pares de chaves se estiver usando chaves assimétricas.

* RSA-2048-19.000
* ECC-P256-91.000

A capacidade irá variar dependendo de atributos de chave específicos definidos no modelo de geração de chave e no número de partições.
