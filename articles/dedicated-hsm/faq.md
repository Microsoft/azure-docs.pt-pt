---
title: Perguntas frequentes - Azure Dedicado HSM | Microsoft Docs
description: Encontre respostas a perguntas comuns sobre o Módulo de Segurança de Hardware Dedicado Azure, tais como informações básicas, interoperabilidade, alta disponibilidade e suporte.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8768acc8d4720a750c4c7f7e0f40340aeeff279d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449057"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Encontre respostas a perguntas comuns sobre o Microsoft Azure Dedicado HSM.

## <a name="the-basics"></a>O Básico

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: O que é um módulo de segurança de hardware (HSM)?

Um Módulo de Segurança de Hardware (HSM) é um dispositivo de computação física utilizado para proteger e gerir chaves criptográficas. As teclas armazenadas em HSMs podem ser utilizadas para operações criptográficas. O material chave permanece seguro em módulos de hardware resistentes à adulteração e inviolá-lo. O HSM só permite aplicações autenticadas e autorizadas para utilizar as teclas. O material chave nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: O que é a oferta de HSM dedicada a Azure?

O Azure Dedicated HSM é um serviço baseado na nuvem que fornece HSMs hospedados em datacenters Azure que estão diretamente ligados à rede virtual de um cliente. Estes HSMs são aparelhos dedicados à rede [Thales Luna 7 HSM.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) São implantados diretamente no espaço de endereço IP privado de um cliente e a Microsoft não tem qualquer acesso à funcionalidade criptográfica dos HSMs. Apenas o cliente tem controlo administrativo e criptográfico completo sobre estes dispositivos. Os clientes são responsáveis pela gestão do dispositivo e podem obter registos de atividade completos diretamente dos seus dispositivos. Os HSMs dedicados ajudam os clientes a cumprir os requisitos de conformidade/regulamentação, tais como FIPS 140-2 Nível 3, HIPAA, PCI-DSS, e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: Que hardware é usado para HSM dedicado?

A Microsoft estabeleceu uma parceria com a Thales para entregar o serviço HSM dedicado à Azure. O dispositivo específico utilizado é o [modelo Thales Luna 7 HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Este dispositivo não só fornece firmware validado [FIPS 140-2 Nível-3,](https://csrc.nist.gov/publications/detail/fips/140/2/final) como também oferece baixa latência, alto desempenho e alta capacidade através de 10 divisórias. 

### <a name="q-what-is-an-hsm-used-for"></a>P: Para que é usado um HSM?

Os HSMs são utilizados para armazenar chaves criptográficas que são usadas para funcionalidades criptográficas como TLS (segurança da camada de transporte), dados de encriptação, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e documentos de assinatura.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Como funciona o HSM dedicado?

Os clientes podem fornecer HSMs em regiões específicas utilizando a interface PowerShell ou a interface de linha de comando. O cliente especifica a rede virtual a que os HSMs serão ligados e uma vez disponibilizados os HSMs estarão disponíveis na sub-rede designada nos endereços IP atribuídos no espaço de endereço IP privado do cliente. Em seguida, os clientes podem ligar-se aos HSMs usando SSH para gestão e administração de aparelhos, configurar ligações de clientes HSM, inicializar HSMs, criar divisórias, definir e atribuir funções como oficial de partição, crypto officer e utilizador de criptomoedas. Em seguida, o cliente utilizará thales fornecendo ferramentas de cliente hSM/SDK/software para realizar operações criptográficas a partir das suas aplicações.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Que software é fornecido com o serviço dedicado HSM?

O Thales fornece todo o software para o dispositivo HSM uma vez provisionado pela Microsoft. O software está disponível no portal de apoio ao [cliente thales.](https://supportportal.thalesgroup.com/csm) Os clientes que utilizam o serviço Dedicado HSM são obrigados a estar registados para suporte thales e têm um ID do Cliente que permite o acesso e o descarregamento de software relevante. O software de cliente suportado é a versão 7.2, que é compatível com a versão validada do firmware 7.0.3 do FIPS 140-2 Nível 3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>P: Que custos adicionais podem ser incorridos com o serviço dedicado HSM?

Os seguintes itens incorrerão num custo extra ao utilizar o serviço Dedicado HSM. 
* A utilização de um dispositivo de backup dedicado no local é viável para ser utilizada com o serviço dedicado HSM, no entanto isso incorrerá num custo extra, e deve ser diretamente proveniente do Thales.
* O HSM dedicado é fornecido com uma licença de 10 divisórias. Se um cliente necessitar de mais divisórias, isso incorrerá num custo extra para licenças adicionais diretamente provenientes do Thales.
* O HSM dedicado requer infraestruturas de rede (VNET, VPN Gateway, Etc.) e recursos como máquinas virtuais para a configuração do dispositivo. Estes recursos adicionais incorrerão em custos adicionais e não estão incluídos no preço dedicado do serviço HSM.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: O Azure Dedicado HSM oferece autenticação baseada em passwords e PED?

Neste momento, o Azure Dedicated HSM apenas fornece hSMs com autenticação baseada em palavra-passe.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: A Azure Dedicado HSM vai receber os meus HSMs para mim?

A Microsoft apenas oferece o modelo Thales Luna 7 HSM A790 através do serviço Dedicado HSM e não pode hospedar nenhum dispositivo fornecido pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: As funcionalidades de suporte de suporte do HSM dedicado ao Azure (PIN/EFT) são suficientes?

O serviço HSM dedicado Azure utiliza Thales Luna 7 HSMs. Estes dispositivos não suportam funcionalidades específicas do HSM de pagamento (como PIN ou EFT) ou certificações. Se pretender que o serviço HSM dedicado da Azure suporte os HSMs de pagamento no futuro, transmita o feedback ao seu Representante de Conta da Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: Em que regiões de Azure está disponível O HSM dedicado?

A partir do final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões estão planeadas e podem ser discutidas através do seu Representante de Conta microsoft.

* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A. Oeste 2
* E.U.A. Centro-Sul
* Sudeste Asiático
* Ásia Leste
* Índia Central
* Índia do Sul
* Leste do Japão
* Oeste do Japão
* Europa do Norte
* Europa Ocidental
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

Utiliza thales fornecendo ferramentas de cliente hSM/SDK/software para realizar operações criptográficas a partir das suas aplicações. O software está disponível no portal de apoio ao [cliente thales.](https://supportportal.thalesgroup.com/csm) Os clientes que utilizam o serviço Dedicado HSM são obrigados a estar registados para suporte thales e têm um ID do Cliente que permite o acesso e o descarregamento de software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: Pode uma aplicação ligar-se ao HSM dedicado a partir de um VNET diferente dentro ou em regiões?

Sim, você precisará usar [o VNET espreitando](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade através de redes virtuais. Para a conectividade entre regiões, deve utilizar [o Gateway VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Posso sincronizar HSM dedicado com HSMs no local?

Sim, pode sincronizar hSMs no local com HSMs dedicado. [A VPN ponto-a-ponto ou a conectividade ponto-a-local](../vpn-gateway/vpn-gateway-about-vpngateways.md) podem ser usadas para estabelecer conectividade com a sua rede no local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Posso encriptar dados utilizados por outros serviços Azure utilizando chaves armazenadas em HSM dedicado?

N.º Os HSMs dedicados Azure só estão acessíveis a partir de dentro da sua rede virtual.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves de um HSM existente no local para o HSM dedicado?

Sim, se tiver no local Thales Luna 7 HSMs. Há vários métodos. Consulte a [documentação do Thales HSM](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: Que sistemas operativos são suportados por software dedicado ao cliente HSM?

* Janelas, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como configurar a minha aplicação de cliente para criar uma configuração de alta disponibilidade com múltiplas divisórias de vários HSMs?

Para ter uma elevada disponibilidade, é necessário configurar a configuração da aplicação do cliente HSM para utilizar divisórias de cada HSM. Consulte a documentação do software do cliente Thales HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: Que mecanismos de autenticação são suportados pelo HSM dedicado?

O Azure Dedicated HSM utiliza [dispositivos A790 modelo A790 do modelo Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) e suportam a autenticação baseada em palavras-passe.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: Que SDKs, APIs, software de cliente está disponível para usar com HSM dedicado?

PKCS#11, Java (JCA/JCE), Microsoft CAPI, e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar chaves de Luna 5/6 HSMs para Azure Dedicados HSMs?

Sim. Contacte o seu representante thales para o guia de migração apropriado do Thales. 

## <a name="using-your-hsm"></a>Utilizando o seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como decido se uso Azure Key Vault ou Azure Dedicado HSM?

O Azure Dedicado HSM é a escolha adequada para as empresas que migram para as aplicações Azure no local que utilizam HSMs. Os HSMs dedicados apresentam uma opção para migrar uma aplicação com alterações mínimas. Se as operações criptográficas forem realizadas no código da aplicação em execução numa VM ou Web App Azure, podem utilizar HSM dedicado. Em geral, o software embrulhado em redução em funcionamento nos modelos IaaS (infraestrutura como serviço), que suportam HSMs como uma loja-chave pode usar O HSM dedicado, como gateway de aplicação ou gestor de tráfego para TLS sem chave, ADCS (Ative Directory Certificate Services), ou ferramentas pki semelhantes, ferramentas/aplicações usadas para a assinatura de documentos, assinatura de código, ou um servidor SQL (IaaS) configurado com TDE (encriptação de base de dados transparente) com chave principal num HSM usando um fornecedor de EKM (gestão de chaves). O Azure Key Vault é adequado para aplicações "born-in-cloud" ou para encriptação em cenários de repouso onde os dados do cliente são processados pela PaaS (plataforma como um serviço) ou por cenários saaS (Software como serviço) como a Chave de Clientes do Office 365, Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store encriptação com chave gerida pelo cliente, encriptação de armazenamento Azure com chave gerida pelo cliente, e Azure SQL com chave gerida pelo cliente.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: Quais os cenários de utilização que melhor se adequam ao HSM dedicado a Azure?

O HSM dedicado a Azure é o mais adequado para cenários de migração. Isto significa que se estiver a migrar aplicações no local para a Azure que já estão a usar HSMs. Isto fornece uma opção de baixa fricção para migrar para Azure com alterações mínimas na aplicação. Se as operações criptográficas forem realizadas no código da aplicação em execução em Azure VM ou Web App, o HSM dedicado pode ser utilizado. Em geral, o software embrulhado em redução em funcionamento em modelos IaaS (infraestrutura como serviço), que suportam HSMs como uma loja chave pode usar O Dedicado HSM, tais como:

* Gateway de aplicações ou gestor de tráfego para TLS sem chave
* ADCS (Serviços de Certificados de Diretório Ativo)
* Ferramentas PKI semelhantes
* Ferramentas/aplicações utilizadas para a assinatura de documentos
* Assinatura de código
* SQL Server (IaaS) configurado com TDE (encriptação de base de dados transparente) com chave principal num HSM utilizando um fornecedor EKM (gestão de chaves extensível)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: O HSM dedicado pode ser utilizado com a chave de cliente do Office 365, Azure Information Protection, Azure Data Lake Store, Disk Encryption, Azure Storage Encryption, Azure SQL TDE?

N.º O HSM dedicado é a provisionado diretamente no espaço ip address privado de um cliente para que não seja acessível por outros serviços Azure ou Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controlo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>P: O cliente obtém controlo exclusivo total sobre os HSMs com dedicado '?

Sim. Cada aparelho HSM é totalmente dedicado a um único cliente e mais ninguém tem controlo administrativo uma vez provisionado e a senha do administrador alterada.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: Que nível de acesso a Microsoft tem para o meu HSM?

A Microsoft não tem qualquer controlo administrativo ou criptográfico sobre o HSM. A Microsoft tem acesso ao nível do monitor através da ligação da porta em série para recuperar a telemetria básica, como a temperatura e a saúde dos componentes. Isto permite à Microsoft fornecer uma notificação proativa de problemas de saúde. Se necessário, o cliente pode desativar esta conta.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>P: O que é a conta de "administrador inquilino" que a Microsoft usa, estou habituado a que o utilizador administrativo seja "administrador" em Thales Luna HSMs?

O dispositivo HSM envia com um utilizador predefinido de administração com a sua senha padrão habitual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo se encontra numa piscina à espera de ser a provisionado pelos clientes. Isto não satisfaria os nossos rigorosos requisitos de segurança. Por esta razão, estabelecemos uma palavra-passe forte, que é descartada no momento do provisionamento. Além disso, no momento de provisionamento criamos um novo utilizador na função de administrador chamado "administrador de inquilinos". Este utilizador tem a palavra-passe padrão e os clientes alteram-na como a primeira ação quando iniciam sessão no dispositivo recém-atado. Este processo garante elevados graus de segurança e mantém a nossa promessa de controlo administrativo exclusivo para os nossos clientes. Note-se que o utilizador "administrador do arrendatário" pode ser utilizado para redefinir a palavra-passe do utilizador administrativo se um cliente preferir utilizar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: A Microsoft ou qualquer pessoa na Microsoft pode aceder às teclas de acesso no meu HSM dedicado?

N.º A Microsoft não tem qualquer acesso às chaves armazenadas no HSM dedicado ao cliente.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Posso atualizar software/firmware em HSMs que me foram atribuídos?

O cliente tem controlo administrativo completo, incluindo a atualização de software/firmware se forem necessárias funcionalidades específicas a partir de diferentes versões de firmware. Antes de estoitar as alterações consulte a Microsoft sobre a sua atualização contactando HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como posso gerir o HSM dedicado?

Pode gerir HSMs dedicados acedendo-os através do SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como posso gerir as divisórias no HSM dedicado?

O software de cliente Thales HSM é utilizado para gerir os HSMs e divisórias.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como monitorar o meu HSM?

Um cliente tem acesso total aos registos de atividades do HSM através do syslog e do SNMP. Um cliente terá de configurar um servidor syslog ou servidor SNMP para receber os registos ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Posso obter registo de acesso completo de todas as operações do HSM da Dedicada HSM?

Sim. Pode enviar registos do aparelho HSM para um servidor sislog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: É possível configurar uma elevada disponibilidade na mesma região ou em várias regiões?

Sim. A configuração e configuração de alta disponibilidade são realizadas no software do cliente HSM fornecido pela Thales. Os HSMs dos mesmos VNET ou outros VNETs na mesma região ou em regiões, ou em instalações HSMs ligados a um VNET utilizando VPN site-to-local ou ponto-a-ponto podem ser adicionados à mesma configuração de alta disponibilidade. Note-se que este material sincroniza apenas e não itens de configuração específicos, tais como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar HSMs da minha rede no local a um grupo de alta disponibilidade com Azure Dedicado HSM?

Sim. Devem satisfazer os elevados requisitos de disponibilidade para [thales Luna 7 HSMs](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar Luna 5/6 HSMs de redes no local a um grupo de alta disponibilidade com Azure Dedicado HSM?

N.º

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade de uma única aplicação?

16 membros de um grupo HA têm testes de aceleração a todo o gás com excelentes resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: O que é o SLA para serviço dedicado hSM?

Não existe nenhuma garantia específica de tempo de antena para o serviço dedicado HSM. A Microsoft garantirá o acesso ao nível da rede ao dispositivo e, por conseguinte, aplica-se a rede Azure padrão.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: Como são protegidos os HSMs em Azure Dedicados ao HSM?

Os centros de dados Azure têm extensos controlos de segurança física e processual. Além disso, os HSMs dedicados estão alojados numa área de acesso restrita ao datacenter. Estas áreas têm controlos de acesso físico adicionais e vigilância de câmaras de vídeo para maior segurança.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: O que acontece se houver uma falha de segurança ou evento de adulteração de hardware?

Serviço dedicado HSM utiliza [eletrodomésticos Thales Luna 7 HSM.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) Estes dispositivos suportam a deteção de adulteração física e lógica. Se houver algum evento de adulteração, os HSMs são automaticamente zeros.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como posso garantir que as chaves nos meus HSMs dedicados não se percam devido a um erro ou a um ataque de infiltrado malicioso?

É altamente recomendado utilizar um dispositivo de backup HSM no local para realizar uma cópia de segurança periódica regular dos HSMs para a recuperação de desastres. Você precisará usar uma ligação VPN peer-to-peer ou site-to-site para uma estação de trabalho no local ligada a um dispositivo de backup HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como posso obter apoio para o HSM dedicado?

O suporte é fornecido tanto pela Microsoft como pelo Thales.  Se tiver algum problema com o hardware ou acesso à rede, levante um pedido de suporte com a Microsoft e se tiver algum problema com a configuração, software e desenvolvimento de aplicações do HSM, faça um pedido de suporte com o Thales. Se tiver um problema indeterminado, levante um pedido de suporte com a Microsoft e, em seguida, o Thales pode ser contratado conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-luna-7-hsm"></a>P: Como consigo o software, documentação e acesso ao cliente orientação de integração para o Thales Luna 7 HSM?

Após o registo do serviço, será fornecido um ID do Cliente Thales que permite a inscrição no portal de apoio ao cliente thales. Isto permitirá o acesso a todo o software e documentação, bem como permitirá pedidos de suporte diretamente com Thales.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Se houver uma vulnerabilidade de segurança encontrada e um patch for lançado pela Thales, quem é responsável pela atualização/remendação do SISTEMA/Firmware?

A Microsoft não tem a capacidade de se ligar aos HSMs atribuídos aos clientes. Os clientes devem atualizar e corrigir os seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: E se eu precisar reiniciar o meu HSM?

O HSM tem uma opção de reboot de linha de comando, no entanto, estamos a experimentar problemas em que o reboot deixa de responder intermitentemente e por isso é recomendado para o reboot mais seguro que você levanta um pedido de suporte com a Microsoft para ter o dispositivo fisicamente reiniciado. 

## <a name="cryptography-and-standards"></a>Criptografia e padrões

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: É seguro armazenar chaves de encriptação para os meus dados mais importantes no HSM dedicado?

Sim, provisões dedicadas do HSM Thales Luna 7 HSMs que são [FIPS 140-2 Nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) validados. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: Que chaves e algoritmos criptográficos são suportados pelo HSM dedicado?

Serviços dedicados de serviço HSM prevêem eletrodomésticos Thales Luna 7 HSM. Eles suportam uma ampla gama de tipos e algoritmos de chaves criptográficas, incluindo: Suporte Full Suite B

* Assimétrico:
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
  * Derivação chave: SP 800-108 Counter Mode
  * Embrulho chave: SP 800-38F
  * Geração aleatória de números: FIPS 140-2 APROVADO DRBG (modo SP 800-90 CTR), em conformidade com o BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: O HSM FIPS 140-2 Nível 3 é validado?

Sim. Serviços dedicados de serviço HSM prevêem aparelhos [A790 modelo A790 do modelo Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) que são [FIPS 140-2 Nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) validados.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: O que preciso de fazer para garantir que opero HSM dedicado no modo validado FIPS 140-2 Nível 3?

O serviço dedicado HSM fornece eletrodomésticos Thales Luna 7 HSM. Estes dispositivos são HSMs validados FIPS 140-2 Nível 3. A configuração, o sistema operativo e o firmware por defeito também são validados pelo FIPS. Não precisa de tomar qualquer medida para o cumprimento do NÍVEL 3 fips 140-2.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: Como é que um cliente garante que quando um HSM é desprovisionado todo o material chave é eliminado?

Antes de solicitar a desprovisionamento, um cliente deve ter zeroizado o HSM usando thales fornecendo ferramentas de cliente HSM.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: Quantas operações criptográficas são suportadas por segundo com HSM dedicado?

Disposições dedicadas do HSM Thales Luna 7 HSMs. Aqui está um resumo do máximo desempenho para algumas operações: 

* RSA-2048: 10.000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: Quantas divisórias podem ser criadas em HSM dedicado?

O [modelo Thales Luna 7 HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) usado inclui uma licença para 10 divisórias no custo do serviço. O dispositivo tem um limite de 100 divisórias e a adição de divisórias até este limite incorreria em custos de licenciamento extra e exigiria a instalação de um novo ficheiro de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: Quantas teclas podem ser suportadas em HSM dedicado?

O número máximo de teclas é uma função da memória disponível. O Thales Luna 7 modelo A790 em uso tem 32MB de memória. Os seguintes números também são aplicáveis aos pares de chaves se utilizarem teclas assimétricas.

* RSA-2048 - 19.000
* ECC-P256 - 91.000

A capacidade variará dependendo dos atributos-chave específicos definidos no modelo de geração chave e no número de divisórias.
