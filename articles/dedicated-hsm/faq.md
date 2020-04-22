---
title: Perguntas frequentes - Azure Dedicado HSM [ Azure Dedicado HSM ] Microsoft Docs
description: Perguntas frequentes que abrangem diferentes tópicos sobre O HSM dedicado ao Azure
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9e759372f01d2a1b48562aef2bace1e8435a67
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683314"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Encontre respostas a perguntas comuns sobre o Microsoft Azure Dedicado HSM.

## <a name="the-basics"></a>O Básico

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: O que é um módulo de segurança de hardware (HSM)?

Um Módulo de Segurança de Hardware (HSM) é um dispositivo de computação física usado para proteger e gerir chaves criptográficas. As chaves armazenadas em HSMs podem ser utilizadas para operações criptográficas. O material-chave permanece seguro em módulos de hardware resistentes a adulteração e adulteração. O HSM só permite aplicações autenticadas e autorizadas para utilizar as teclas. O material-chave nunca sai do limite de proteção hsm.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: O que é a oferta de HSM dedicada ao Azure?

O Azure Dedicated HSM é um serviço baseado na nuvem que fornece HSMs hospedados em datacenters Azure que estão diretamente ligados à rede virtual de um cliente. Estes HSMs são aparelhos de rede dedicados (Rede SafeNet HSM 7 Model A790 da Gemalto). São implantados diretamente no espaço de endereçoIP privado de um cliente e a Microsoft não tem qualquer acesso à funcionalidade criptográfica dos HSMs. Apenas o cliente tem total controlo administrativo e criptográfico sobre estes dispositivos. Os clientes são responsáveis pela gestão do dispositivo e podem obter registos de atividade completa diretamente dos seus dispositivos. HSMs dedicados ajudam os clientes a cumprir os requisitos de conformidade/regulação tais como FIPS 140-2 Level 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: Que hardware é usado para O HSM dedicado?

A Microsoft estabeleceu uma parceria com a Gemalto para fornecer o serviço Azure Dedicado HSM. O dispositivo específico utilizado é o [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Este dispositivo não só fornece firmware validado de Nível 3 fips 140-2, como também oferece baixa latência, alto desempenho e alta capacidade através de 10 divisórias. 

### <a name="q-what-is-an-hsm-used-for"></a>P: Para que é utilizado um HSM?

Os HSMs são usados para armazenar chaves criptográficas que são usadas para funcionalidades criptográficas como TLS (segurança da camada de transporte), encriptação de dados, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e documentos de assinatura.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Como funciona o HSM dedicado?

Os clientes podem fornecer HSMs em regiões específicas usando powerShell ou interface de linha de comando. O cliente especifica a rede virtual a que os HSMs estarão ligados e uma vez provisionados os HSMs estarão disponíveis na subnet designada em endereços IP atribuídos no espaço de endereçoip privado do cliente. Em seguida, os clientes podem ligar-se aos HSMs usando SSH para gestão e administração de aparelhos, configurar ligações ao cliente HSM, inicializar HSMs, criar divisórias, definir e atribuir funções como oficial de partição, oficial de criptoe e utilizador de cripto. Em seguida, o cliente utilizará a Gemalto forneceu ferramentas de cliente HSM/SDK/software para realizar operações criptográficas a partir das suas aplicações.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Que software é fornecido com o serviço HSM dedicado?

A Gemalto fornece todo o software para o dispositivo HSM uma vez aprovisionado pela Microsoft. O software está disponível no portal de apoio ao [cliente gemalto.](https://supportportal.gemalto.com/csm/) Os clientes que utilizam o serviço HSM dedicado são obrigados a estar registados para suporte gemalto e têm um ID do Cliente que permite o acesso e o download de software relevante. O software de cliente suportado é a versão 7.2, que é compatível com a versão 7.0.3 validada do Nível 3 do FIPS 140-2. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: O Azure Dedicado HSM oferece autenticação baseada em Palavras-passe e PED?

Neste momento, o Azure Dedicado HSM apenas fornece HSMs com autenticação baseada em palavra-passe.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: Azure Dedicado HSM receberá os meus HSMs para mim?

A Microsoft apenas oferece o HSM da Rede Gemalto SafeNet Luna através do serviço HSM dedicado e não pode alojar quaisquer dispositivos fornecidos pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: O pagamento dedicado ao apoio ao HSM (PIN/EFT) do Azure dispõe?

O serviço Azure Dedicado HSM utiliza dispositivos SafeNet Luna Network HSM 7 (modelo A790). Estes dispositivos não suportam o pagamento de funcionalidades específicas hSM (tais como PIN ou EFT) ou certificações. Se quiser que o serviço Azure Dedicated HSM suporte futuramente hSMs de pagamento, transmita o feedback para o seu Representante de Conta Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: Em que regiões azure está disponível o HSM dedicado?

A partir do final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões estão planeadas e podem ser discutidas através do seu Representante de Conta microsoft.

* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A.Oeste 2
* E.U.A. Centro-Sul
* Ásia Sudeste
* Ásia Leste
* Índia Central
* Índia do Sul
* Leste do Japão
* Oeste do Japão
* Europa do Norte
* Europa ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Austrália Sudeste
* Suíça Norte
* Suíça Oeste
* US Gov - Virginia
* US Gov - Texas

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: Como é que a minha aplicação se liga a um HSM dedicado?

Utiliza a Gemalto forneceu ferramentas de cliente HSM/SDK/software para realizar operações criptográficas a partir das suas aplicações. O software está disponível no portal de apoio ao [cliente gemalto.](https://supportportal.gemalto.com/csm/) Os clientes que utilizam o serviço HSM dedicado são obrigados a estar registados para suporte gemalto e têm um ID do Cliente que permite o acesso e o download de software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: Uma aplicação pode ligar-se ao HSM dedicado a partir de um VNET diferente em ou em todas as regiões?

Sim, você precisará usar [o vNET espreitando](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade através de redes virtuais. Para a conectividade transversal, deve utilizar [o VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Posso sincronizar HSM dedicado com HSMs no local?

Sim, pode sincronizar hSMs no local com HSMdedicado. A [VPN ponto-a-ponto ou a conectividade ponto-a-local](../vpn-gateway/vpn-gateway-about-vpngateways.md) podem ser usadas para estabelecer conectividade com a sua rede no local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Posso encriptar os dados utilizados por outros serviços do Azure utilizando chaves armazenadas em HSM dedicado?

Não. Os HSMs Dedicados Azure só são acessíveis a partir de dentro da sua rede virtual.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves de um HSM existente no local para HSM dedicado?

Sim, se tiver no local Gemalto SafeNet HSMs. Há vários métodos. Consulte a documentação do Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: Que sistemas operativos são suportados por software de cliente HSM dedicado?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como configurar a aplicação do meu cliente para criar uma configuração de alta disponibilidade com múltiplas divisórias de vários HSMs?

Para ter uma elevada disponibilidade, precisa de configurar a configuração da sua aplicação de cliente HSM para utilizar divisórias de cada HSM. Consulte a documentação do software do cliente Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: Que mecanismos de autenticação são suportados pelo HSM dedicado?

O Azure Dedicado HSM utiliza aparelhos SafeNet Network HSM 7 (modelo A790) e suportam a autenticação baseada em palavra-passe.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: Que SDKs, APIs, software cliente está disponível para usar com HSM dedicado?

PKCS#11, Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar chaves de Luna 5/6 HSMs para HSMs dedicados ao Azure?

Sim. Consulte o guia de migração gemalto. 

## <a name="using-your-hsm"></a>Usando o seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como decido se uso o Cofre de Chave Azure ou o Azure Dedicado HSM?

O Azure Dedicated HSM é a escolha adequada para as empresas que migram para as aplicações no local do Azure que utilizam HSMs. HSMs dedicados apresentam uma opção de migrar uma aplicação com alterações mínimas. Se as operações criptográficas forem realizadas no código da aplicação em execução numa VM Ou Web App Azure, podem utilizar HSM dedicado. Em geral, software encolhimento em execução em modelos IaaS (infraestrutura como serviço), que suportam HSMs como uma loja chave pode usar O HSM dedicado, como gateway de aplicação ou gestor de tráfego para TLS sem chave, ADCS (Serviços de Certificado de Diretório Ativo), ou ferramentas PKI semelhantes, ferramentas/aplicações usadas para a assinatura de documentos, assinatura de código ou um Servidor SQL (IaaS) configurado com TDE (encriptação de base de dados transparente) com chave principal num HSM usando um Fornecedor EKM (gestão de chaves extensíveis). O Azure Key Vault é adequado para aplicações "nanuvem" ou para encriptação em cenários de repouso onde os dados dos clientes são processados pela PaaS (plataforma como serviço) ou saaS (Software como serviço) como a Chave do Cliente do Office 365, Proteção de Informação Azure, Encriptação de Disco Azure, encriptação azure Data Lake Store com chave gerida pelo cliente, encriptação de armazenamento Azure com chave gerida pelo cliente, e Azure SQL com chave gerida pelo cliente.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: Quais os cenários de utilização que melhor se adequam ao HSM dedicado ao Azure?

O Azure Dedicado HSM é o mais adequado para cenários de migração. Isto significa que se estiver a migrar para o Local aplicações para o Azure que já estão a utilizar HSMs. Isto proporciona uma opção de baixa fricção para migrar para Azure com alterações mínimas na aplicação. Se as operações criptográficas forem realizadas no código da aplicação em execução no Azure VM ou na Web App, pode ser utilizado hSM dedicado. Em geral, o software entriscada em funcionamento nos modelos IaaS (infraestrutura como serviço), que suporta mHS como uma loja-chave pode usar O HSM dedicado, tais como:

* Gateway de aplicação ou gestor de tráfego para TLS sem chave
* ADCS (Serviços de Certificado de Diretório Ativo)
* Ferramentas PKI semelhantes
* Ferramentas/aplicações utilizadas para a assinatura de documentos
* Assinatura de código
* SQL Server (IaaS) configurado com TDE (encriptação de base de dados transparente) com chave principal num HSM utilizando um provedor EKM (gestão de chaves extensíveis)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: HSM dedicado pode ser usado com Chave de Cliente do Office 365, Azure Information Protection, Azure Data Lake Store, Disk Encryption, Encriptação de Armazenamento Azure, TDE Azure SQL?

Não. O HSM dedicado é aprovisionado diretamente no espaço ip address privado de um cliente para que não seja acessível por outros serviços Azure ou Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controlo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P: O cliente obtém controlo exclusivo total sobre os HSMs com HSMs dedicados?

Sim. Cada aparelho HSM é totalmente dedicado a um único cliente e mais ninguém tem controlo administrativo uma vez provisionado e a palavra-passe do administrador alterada.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: Que nível de acesso a Microsoft tem ao meu HSM?

A Microsoft não tem qualquer controlo administrativo ou criptográfico sobre o HSM. A Microsoft tem acesso ao nível do monitor através da ligação por porta em série para recuperar a telemetria básica, como a temperatura e a saúde dos componentes. Isto permite que a Microsoft forneça uma notificação proativa de problemas de saúde. Se necessário, o cliente pode desativar esta conta.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P: O que é que a conta "tenantadmin" que a Microsoft utiliza, estou habituado ao facto de o utilizador administrador estar a ser "administrador" em HSMs SafeNet?

O dispositivo HSM envia com um utilizador padrão de administração com a sua senha padrão habitual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo está numa piscina à espera de ser aprovisionado pelos clientes. Isto não cumpriria os nossos rigorosos requisitos de segurança. Por esta razão, definimos uma senha forte, que é descartada no momento do fornecimento. Além disso, no tempo de provisionamento criamos um novo utilizador na função de administrador chamado "tenantadmin". Este utilizador tem a senha padrão e os clientes alteram-na como a primeira ação ao iniciar sessão no dispositivo recém-provisionado. Este processo garante elevados graus de segurança e mantém a nossa promessa de controlo administrativo exclusivo para os nossos clientes. Note-se que o utilizador "tenantadmin" pode ser utilizado para redefinir a palavra-passe do utilizador administrativo se um cliente preferir utilizar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: A Microsoft ou alguém da Microsoft pode aceder a chaves no meu HSM dedicado?

Não. A Microsoft não tem acesso às chaves armazenadas no HSM dedicado ao cliente.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Posso atualizar software/firmware em HSMs que me foram atribuídos?

Para obter o melhor suporte, a Microsoft recomenda vivamente que não atualize software/firmware no HSM. No entanto, o cliente tem controlo administrativo completo, incluindo a atualização de software/firmware se forem necessárias funcionalidades específicas a partir de diferentes versões de firmware. Antes de fazer alterações, as implicações devem ser entendidas, pois isso poderia, por exemplo, afetar o estado validado do FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como posso gerir o HSM dedicado?

Pode gerir HSMs dedicados acedendo-os utilizando SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como posso gerir divisórias no HSM dedicado?

O software cliente Gemalto HSM é usado para gerir os HSMs e divisórias.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como monitorizo o meu HSM?

Um cliente tem acesso total aos registos de atividade sysm e SNMP. Um cliente terá de configurar um servidor syslog ou servidor SNMP para receber os registos ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Posso obter o registo de acesso completo de todas as operações da HSM da HSM dedicada?

Sim. Pode enviar registos do aparelho HSM para um servidor syslog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: É possível configurar alta disponibilidade na mesma região ou em várias regiões?

Sim. A configuração e configuração de alta disponibilidade são realizadas no software cliente HSM fornecido pela Gemalto. HSMs do mesmo VNET ou outros VNETs na mesma região ou em todas as regiões, ou no local HSMs ligados a um VNET usando VPN local-a-local ou ponto-a-ponto pode ser adicionado à mesma configuração de alta disponibilidade. Note-se que este sincroniza apenas o material chave e não itens de configuração específicos, tais como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar HSMs da minha rede no local a um grupo de alta disponibilidade com o Azure Dedicado HSM?

Sim. Devem satisfazer os elevados requisitos de disponibilidade para a SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar A Luna 5/6 HSMs das redes no local a um grupo de alta disponibilidade com o Azure Dedicado HSM?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade de uma única aplicação?

16 membros de um grupo HA têm testes de aceleração completa com excelentes resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: O que é o SLA para serviço HSM dedicado?

Não existe uma garantia específica de tempo de apagamento prevista para o serviço HSM dedicado. A Microsoft garantirá o acesso ao nível da rede ao dispositivo e, consequentemente, aplicam-se sLAs de rede Azure padrão.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: Como estão protegidos os HSMs em HSM dedicado ao Azure?

Os centros de dados Azure têm controlos de segurança física e processual extensivos. Além disso, os HSMs dedicados estão alojados numa área de acesso mais restrita do datacenter. Estas áreas têm controlos adicionais de acesso físico e vigilância de câmaras de vídeo para maior segurança.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: O que acontece se houver uma violação de segurança ou um evento de adulteração de hardware?

O serviço HSM dedicado utiliza aparelhos SafeNet Network HSM 7. Estes aparelhos suportam a deteção de adulteração física e lógica. Se houver algum evento de adulteração, os HSMs são automaticamente zeroizados.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como posso garantir que as chaves dos meus HSMs dedicados não se percam devido a erro ou a um ataque de infiltrado sufoco malicioso?

É altamente recomendado utilizar um dispositivo de backup HSM no local para realizar cópias de segurança periódicas regulares dos HSMs para recuperação de desastres. Você precisará usar uma ligação VPN peer-to-site ou site-to-site para uma estação de trabalho no local ligada a um dispositivo de backup HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como posso obter apoio para HSM dedicado?

O suporte é fornecido tanto pela Microsoft como pela Gemalto.  Se tiver algum problema com o hardware ou acesso à rede, levante um pedido de suporte com a Microsoft e se tiver algum problema com a configuração, software e desenvolvimento de aplicações HSM, levante um pedido de suporte com gemalto. Se tiver um problema indeterminado, levante um pedido de suporte com a Microsoft e, em seguida, Gemalto pode ser contratado conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Como obter o software, documentação e acesso ao software do cliente, documentação e acesso à orientação de integração para o SafeNet Luna 7 HSM?

Após a inscrição para o serviço, será fornecido um ID do Cliente Gemalto que permite a inscrição no portal de apoio ao cliente gemalto. Isto permitirá o acesso a todo o software e documentação, bem como permitir ás solicitações de suporte diretamente com a Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Se houver uma vulnerabilidade de segurança encontrada e um patch for lançado pela Gemalto, quem é responsável pela atualização/remendação de OS/Firmware?

A Microsoft não tem a capacidade de se ligar aos HSMs atribuídos aos clientes. Os clientes devem atualizar e corrigir os seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: E se eu precisar reiniciar o meu HSM?

O HSM tem uma opção de reinicialização da linha de comando, no entanto, estamos a experimentar problemas de reinicialização de hang intermitentemente e por isso é recomendado para o reboot mais seguro que você levantar um pedido de suporte com a Microsoft para ter o dispositivo fisicamente reiniciado. 

## <a name="cryptography-and-standards"></a>Criptografia e padrões

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: É seguro armazenar chaves de encriptação para os meus dados mais importantes no HSM dedicado?

Sim, disposições HSM dedicadas safeNet Network HSM 7 aparelhos que usam HSMs validados de nível 3 FIPS 140-2. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: Que chaves e algoritmos criptográficos são suportados por HSM dedicado?

Disposições de serviço HSM dedicadas safeNet Network HSM 7. Eles suportam uma ampla gama de tipos e algoritmos de chave criptográfica, incluindo: Suporte Full Suite B

* Assimétrica:
  * RSA
  * DSA
  * Diffie-Hellman
  * Curva elíptica
  * Criptografia (ECDSA, ECDH, Ed25519, ECIES) com curvas nomeadas, definidas pelo utilizador e Brainpool, KCDSA
* Simétrica:
  * AES-GCM
  * Triplo DES
  * DES
  * ARIA, SEMENTE
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Derivação chave: Modo de contra-ataque SP800-108
  * Embrulho da chave: SP800-38F
  * Geração aleatória de números: FIPS 140-2 HOMOX aprovado (modo SP 800-90 CTR), cumprindo com BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: O HSM FIPS 140-2 Nível 3 é validado?

Sim. Disposições dedicadas ao serviço HSM safeNet Network HSM 7 aparelhos que utilizam HSMs validados de nível 3 FIPS 140-2.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: O que preciso de fazer para ter a certeza de que opero HSM dedicado no modo validado de Nível 3 FIPS 140-2?

O serviço Dedicado HSM disponibiliza eletrodomésticos SafeNet Luna Network HSM 7. Estes aparelhos utilizam HSMs validados de nível 3 FIPS 140-2. A configuração, o sistema operativo e o firmware implantados predefinidos também são validados pelo FIPS. Não precisa de tomar qualquer medida para a conformidade do Nível 3 do FIPS 140-2.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: Como é que um cliente garante que quando um HSM é desprovisionado todo o material chave é eliminado?

Antes de solicitar a desprovisionamento, um cliente deve ter zeroizado o HSM utilizando a Gemalto forneceu ferramentas ao cliente HSM.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: Quantas operações criptográficas são suportadas por segundo com HSM dedicado?

Disposições HSM dedicadas SafeNet Network HSM 7 (modelo A790). Aqui está um resumo do desempenho máximo para algumas operações: 

* RSA-2048: 10.000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: Quantas divisórias podem ser criadas em HSM dedicado?

O SafeNet Luna HSM 7 modelo A790 utilizado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 divisórias e adicionar divisórias até este limite incorreria em custos de licenciamento adicionais e exigiria a instalação de um novo ficheiro de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: Quantas chaves podem ser suportadas no HSM dedicado?

O número máximo de teclas é uma função da memória disponível. O Modelo A790 SafeNet Luna 7 em uso tem 32MB de memória. Os seguintes números também são aplicáveis aos pares-chave se utilizarem chaves assimétricas.

* RSA-2048 - 19.000
* ECC-P256 - 91.000

A capacidade variará dependendo de atributos-chave específicos definidos no modelo de geração chave e número de divisórias.
