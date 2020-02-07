---
title: Perguntas mais frequentes sobre - HSM dedicada do Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre que aborda tópicos diferentes no HSM dedicada do Azure
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
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 947dc6eb8060d2d229ee7984f719cb837e638490
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045930"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Encontre respostas a perguntas comuns sobre o HSM dedicados do Microsoft Azure.

## <a name="the-basics"></a>As noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: o que é um módulo de segurança de hardware (HSM)?

Um módulo de segurança de Hardware (HSM) é um dispositivo de computação físico utilizado para salvaguardar e gerir chaves criptográficas. Chaves armazenadas em HSMs podem ser utilizadas para operações criptográficas. O material de chave com segurança permanece em módulos de hardware resistente a adulterações, adulterar-evidente. O HSM só permite autenticado e autorizado a aplicações para utilizar as chaves. O material das chaves nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: O que é a oferta de HSM dedicada ao Azure?

HSM dedicada do Azure é um serviço baseado na nuvem que fornece HSMs alojados em datacenters do Azure que estejam diretamente ligados à rede virtual de um cliente. Estes HSMs são aplicações de rede dedicado (da Gemalto SafeNet rede HSM 7 modelo A790). Elas serem implantadas diretamente para o espaço de endereços IP privado uma parte dos clientes e a Microsoft não tem qualquer acesso à funcionalidade de criptografia dos HSMs. Apenas o cliente tem controle completo administrativa e criptografia sobre estes dispositivos. Os clientes são responsáveis pela gestão do dispositivo e podem obter registos de atividade completo diretamente a partir dos seus dispositivos. HSMs dedicados ajudam os clientes a cumprir os requisitos de conformidade regulamentar, como FIPS 140-2 nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: Que hardware é usado para O HSM dedicado?

A Microsoft estabeleceu uma parceria com a Gemalto para fornecer o serviço Azure Dedicado HSM. O dispositivo específico utilizado é o [SafeNet Luna Network HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Este dispositivo não apenas fornece FIPS 140-2 nível 3 validado firmware, mas também oferece baixa latência, elevado desempenho e elevada capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P: para o que é utilizado um HSM?

Os HSMs são utilizados para armazenar chaves criptográficas que, por sua vez, são utilizadas para proporcionar funcionalidades criptográficas, como SSL (Secure Sockets Layer), encriptação de dados, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e assinar documentos.

### <a name="q-how-does-dedicated-hsm-work"></a>P: como funciona o HSM dedicados?

Os clientes podem aprovisionar HSMs em regiões específicas com o PowerShell ou a interface de linha de comandos. O cliente especifica qual rede virtual serão ligados ao e uma vez provisionados os HSMs os HSMs estarão disponíveis na sub-rede designada em endereços IP atribuídos no espaço de endereços IP privados do cliente. Em seguida, os clientes podem ligar-se para os HSMs através de SSH para a gestão da aplicação e administração, configurar ligações de cliente HSM, inicializar HSMs, criar partições, definir e atribuir funções como diretor de partição, diretor de criptografia e utilizador criptografia. Em seguida, o cliente irá utilizar Gemalto fornecida HSM ferramentas/SDK/software de cliente para efetuar operações de criptografia a partir das suas aplicações.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: qual software é fornecido com o serviço de HSM dedicados?

Gemalto fornece todo o software para o dispositivo HSM aprovisionado uma vez pela Microsoft. O software está disponível no portal de apoio ao [cliente gemalto.](https://supportportal.gemalto.com/csm/) Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante. O software de cliente suportado é a versão 7.2, que é compatível com a versão 7.0.3 validada do Nível 3 do FIPS 140-2. 

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
* E.U.A. Centro-Sul
* Sudeste asiático
* Ásia Oriental
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
* Sudeste da Austrália

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: como é que meu aplicativo ligar-se um HSM dedicados?

Utilize Gemalto fornecida HSM ferramentas/SDK/software de cliente para realizar operações criptográficas das suas aplicações. O software está disponível no portal de apoio ao [cliente gemalto.](https://supportportal.gemalto.com/csm/) Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: uma aplicação ligar ao HSM dedicados a partir de uma VNET diferente num ou em regiões?

Sim, você precisará usar [o vNET espreitando](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer conectividade através de redes virtuais. Para a conectividade transversal, deve utilizar [o VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: sincronizar HSM dedicado com o HSM no local?

Sim, pode sincronizar HSMs no local com o HSM dedicados. A [VPN ponto-a-ponto ou a conectividade ponto-a-local](../vpn-gateway/vpn-gateway-about-vpngateways.md) podem ser usadas para estabelecer conectividade com a sua rede no local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: encriptar dados utilizados por outros serviços do Azure utilizando chaves armazenadas em HSM dedicados?

Não. São apenas acessíveis a partir de dentro da sua rede virtual do Azure de HSMs dedicados.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves a partir de um HSM no local existente para o HSM dedicados?

Sim, se tiver no local da Gemalto SafeNet HSMs. Existem vários métodos. Consulte a documentação da Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: quais são os sistemas operativos são suportados pelo software de cliente de HSM dedicados?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: como posso configurar a minha aplicação de cliente para criar uma configuração de elevada disponibilidade com várias partições a partir dos vários HSMs?

Para ter uma elevada disponibilidade, terá de definir a configuração de aplicação de cliente do HSM para utilizar partições cada HSM. Consulte a documentação de software de cliente da Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: quais mecanismos de autenticação são suportados por HSM dedicados?

HSM dedicada do Azure utiliza aplicações de 7 de HSM de rede SafeNet (modelos A790) e que suportam autenticação baseada em palavra-passe.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: qual software de cliente de SDKs, APIs, está disponível para utilização com o HSM dedicados?

PKCS #11, o Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar as chaves de Luna 5/6 HSMs para HSMs do Azure dedicado?

Sim. Consulte o guia de migração gemalto. 

## <a name="using-your-hsm"></a>Utilizar o seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: como posso decidir se deve utilizar o Azure Key Vault ou o HSM do Azure dedicado?

HSM dedicada do Azure é a opção adequada para as empresas a migrar para aplicações do Azure no local que utilizam o HSMs. HSMs dedicados apresentam uma opção para migrar uma aplicação com alterações mínimas. Se as operações criptográficas são executadas no código do aplicativo em execução numa VM do Azure ou a aplicação Web, eles podem usar o HSM dedicados. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, por exemplo, o Gestor de gateway ou o tráfego de aplicações para SSL sem chave, o ADCS (Active Directory Certificate Services), ou ferramentas PKI similares, ferramentas/aplicações utilizadas para assinatura de documentos, a assinatura de código ou um SQL Server (IaaS) configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível). O Azure Key Vault é adequado para aplicações "born-in-cloud" ou para a encriptação em cenários de rest onde os dados dos clientes são processados por PaaS (plataforma como serviço) ou a cenários de SaaS (Software como serviço) como chave de cliente do Office 365, Azure Information Protection , O azure Disk Encryption, a encriptação do Azure Data Lake Store com encriptação principal, de armazenamento do Azure gerida pelo cliente com o cliente gerido chave e SQL do Azure com o cliente gerido chave.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: quais cenários de utilização que melhor se adequar às HSM do Azure dedicado?

HSM dedicada do Azure é mais adequado para cenários de migração. Isso significa que, se estiver a migrar aplicações no local para o Azure que já estão a utilizar HSMs. Isso fornece uma opção de baixa fricção para migrar para o Azure com alterações mínimas à aplicação. Se as operações criptográficas são executadas no código do aplicativo em execução na VM do Azure ou a aplicação Web, HSM dedicados podem ser utilizado. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, tais como:

* Gestor de tráfego ou gateway de aplicação para SSL sem chave
* ADCS (Serviços de certificados do Active Directory)
* Ferramentas similares de PKI
* Ferramentas/aplicações utilizadas para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS), configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: dedicado HSM ser utilizado com a chave de cliente do Office 365, Azure Information Protection, Azure Data Lake Store, encriptação de disco, encriptação de armazenamento do Azure, Azure SQL TDE?

Não. HSM dedicados é aprovisionado diretamente no espaço de endereço IP privado de um cliente para que ele faz não está acessível por outros serviços do Azure ou a Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controlo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P: o cliente obter um controlo exclusivo completa sobre os HSMs com HSMs dedicados?

Sim. Cada aplicação de HSM está totalmente dedicada de um único cliente, e ninguém tem controle administrativo, uma vez aprovisionado e a palavra-passe de administrador foi alterado.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: o nível de acesso a Microsoft tem para o meu HSM?

Microsoft não tem qualquer controlo administrativo ou criptografia sobre o HSM. A Microsoft ter acesso de nível de monitor através de ligação de porta serial para obter telemetria básica, tais como o estado de funcionamento do componente e de temperatura. Isso permite que a Microsoft fornece notificação proativa de problemas de estado de funcionamento. Se necessário, o cliente pode desativar esta conta.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P: O que é que a conta "tenantadmin" que a Microsoft utiliza, estou habituado ao facto de o utilizador administrador estar a ser "administrador" em HSMs SafeNet?

O dispositivo HSM envia com um utilizador padrão de administração com a sua senha padrão habitual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo está numa piscina à espera de ser aprovisionado pelos clientes. Isto não cumpriria os nossos rigorosos requisitos de segurança. Por esta razão, definimos uma senha forte, que é descartada no momento do fornecimento. Além disso, no tempo de provisionamento criamos um novo utilizador na função de administrador chamado "tenantadmin". Este utilizador tem a senha padrão e os clientes alteram-na como a primeira ação ao iniciar sessão no dispositivo recém-provisionado. Este processo garante elevados graus de segurança e mantém a nossa promessa de controlo administrativo exclusivo para os nossos clientes. Note-se que o utilizador "tenantadmin" pode ser utilizado para redefinir a palavra-passe do utilizador administrativo se um cliente preferir utilizar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: pode Microsoft ou para alguém da chaves de acesso da Microsoft no meu HSM dedicados?

Não. Microsoft não tem qualquer acesso às chaves armazenadas no cliente alocado HSM dedicados.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: posso atualizar o software/firmware nos HSMs alocado para mim?

Para obter o melhor suporte, a Microsoft recomenda vivamente não fazer a atualização de software/firmware no HSM. No entanto, o cliente ter controle administrativo completo, incluindo a atualização de software/firmware se são necessárias de versões de firmware diferentes funcionalidades específicas. Antes de fazer alterações, as implicações devem ser entendidas, pois isso poderia, por exemplo, afetar o estado validado do FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: como posso gerir HSM dedicados?

Pode gerir HSMs dedicados ao aceder às mesmas através de SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: como posso gerir partições no HSM dedicados?

O software de cliente de Gemalto HSM é utilizado para gerir os HSMs e partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: como posso monitorizar o meu HSM?

Um cliente tem acesso total aos registos de atividades HSM através do syslog e SNMP. Um cliente tem de configurar um servidor syslog ou um servidor SNMP para receber os registos ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: posso obter o registo de acesso total de todas as operações de HSM de HSM dedicados?

Sim. Pode enviar registos a partir da aplicação do HSM para um servidor syslog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: É possível configurar alta disponibilidade na mesma região ou em várias regiões?

Sim. Configuração de elevada disponibilidade e o programa de configuração são executadas no software de cliente HSM fornecido pelo Gemalto. HSMs do mesmo VNET ou outros VNETs na mesma região ou em todas as regiões, ou no local HSMs ligados a um VNET usando VPN local-a-local ou ponto-a-ponto pode ser adicionado à mesma configuração de alta disponibilidade. Note-se que este sincroniza apenas o material chave e não itens de configuração específicos, tais como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar HSMs da minha rede no local a um grupo de alta disponibilidade com o Azure Dedicado HSM?

Sim. Têm de cumprir os requisitos de elevada disponibilidade para o 7 de HSM de rede de Luna SafeNet.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar A Luna 5/6 HSMs das redes no local a um grupo de alta disponibilidade com o Azure Dedicado HSM?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: HSMs muitos como posso adicionar para a mesma configuração de elevada disponibilidade de um único aplicativo?

16 membros de um grupo HA têm testes de aceleração completa com excelentes resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: qual é o SLA para o serviço HSM dedicados?

Não existe uma garantia específica de tempo de apagamento prevista para o serviço HSM dedicado. Microsoft irá garantir o acesso de nível de rede para o dispositivo e, por conseguinte, aplicam o padrão SLAs de rede do Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: como os HSMs são utilizados no Azure dedicado protegidas por HSM?

Os datacenters do Azure tem controlos de segurança física e procedimentos. Além disso ou HSMs dedicados estão alojados numa área de ainda mais o acesso restrito do Centro de dados. Essas áreas tem controlos de acesso físico adicionais e vigilância de câmera de vídeo para obter segurança adicional.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: o que acontece se ocorrer uma violação de segurança ou do hardware adulteração eventos?

Serviço HSM dedicado utiliza aplicações de 7 de HSM de rede SafeNet. Estas aplicações suportam a deteção de adulteração física e lógica. Se já existe um evento de adulterações que os HSMs são zeroized automaticamente.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: como garantir que as chaves na minha HSMs dedicado não são perdidas devido a erro ou um ataque insider malicioso?

É altamente recomendado a utilizar um dispositivo de cópia de segurança de HSM no local para efetuar cópia de segurança periódica normal dos HSMs para recuperação após desastre. Terá de utilizar uma ligação de VPN ponto a ponto ou site a site para uma estação de trabalho no local ligada a um dispositivo de cópia de segurança do HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: como posso obter suporte para o HSM dedicados?

O suporte é fornecido tanto pela Microsoft como pela Gemalto.  Se tiver algum problema com o hardware ou acesso à rede, levante um pedido de suporte com a Microsoft e se tiver algum problema com a configuração, software e desenvolvimento de aplicações HSM, levante um pedido de suporte com gemalto. Se tiver um problema indeterminado, levante um pedido de suporte com a Microsoft e, em seguida, Gemalto pode ser contratado conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Como obter o software, documentação e acesso ao software do cliente, documentação e acesso à orientação de integração para o SafeNet Luna 7 HSM?

Após a inscrição para o serviço, será fornecido um ID do Cliente Gemalto que permite a inscrição no portal de apoio ao cliente gemalto. Isto permitirá o acesso a todo o software e documentação, bem como permitir ás solicitações de suporte diretamente com a Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: se houver uma vulnerabilidade de segurança encontrada e um patch é lançado pela Gemalto, que é responsável pela aplicação de patches e atualizar o sistema operacional/Firmware?

Microsoft não tem a capacidade de conexão para HSMs alocadas para os clientes. Os clientes tem de atualizar e aplicar um patch seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: E se eu precisar reiniciar o meu HSM?

O HSM tem uma opção de reinicialização da linha de comando, no entanto, estamos a experimentar problemas de reinicialização de hang intermitentemente e por isso é recomendado para o reboot mais seguro que você levantar um pedido de suporte com a Microsoft para ter o dispositivo fisicamente reiniciado. 

## <a name="cryptography-and-standards"></a>Padrões e criptografia

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: é seguro armazenar chaves de encriptação para meus dados mais importantes num HSM dedicados?

Sim, o HSM dedicados Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: para que as chaves criptográficas e os algoritmos são suportados por HSM dedicados?

Aplicações de provisões 7 de HSM de rede SafeNet do serviço HSM dedicadas. Eles oferecem suporte a uma ampla variedade de tipos de chaves criptográficos e algoritmos, incluindo: suporte completo Suite B

* Assimétrica:
  * RSA
  * DSA
  * Diffie-Hellman
  * Criptografia de curva elíptica
  * Criptografia (ECDSA, ECDH, Ed25519, ECIES) com nomeado, definida pelo utilizador e as curvas de Brainpool, KCDSA
* Simétrica:
  * AES-GCM
  * DES triplo
  * DES
  * ARIA SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Resumo de mensagens com hash / / HMAC: SHA-1, SHA-2, SM3
  * Derivação de chaves: O modo de contador de SP800 108
  * Chave de encapsulamento de aplicações: SP800-38F
  * Geração de números aleatórios: FIPS 140-2 aprovado DRBG (modo de SP 800 90 CTR), o cumprimento BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: É dedicado HSM FIPS que 140-2 nível 3 validada?

Sim. Serviço HSM dedicado Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: o que preciso fazer para se certificar de que eu operar HSM dedicados no FIPS 140-2 nível 3 validado modo?

O serviço de HSM dedicados Aprovisiona aparelhos de 7 de HSM de rede de Luna SafeNet. HSMs validadas por estes uso de aparelhos FIPS 140-2 nível 3. A configuração predefinida implementada, o sistema operativo e o firmware também são FIPS validada. Não é necessário efetuar qualquer ação de conformidade do FIPS 140-2 nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: Como é que um cliente garante que quando um HSM é desprovisionado todo o material chave é eliminado?

Antes de solicitar o desaprovisionamento, um cliente tem de ter a zeroized o HSM através da Gemalto fornecida ferramentas de cliente do HSM.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: como muitas operações criptográficas são suportadas por segundo com o HSM dedicados?

Aplicações de provisões 7 de HSM de rede SafeNet HSM dedicadas (modelo A790). Aqui está um resumo de desempenho máximo para algumas operações: 

* RSA-2048: 10 000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: o número de partições podem ser criadas no HSM dedicados?

O SafeNet Luna HSM 7 modelo A790 utilizado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 divisórias e adicionar divisórias até este limite incorreria em custos de licenciamento adicionais e exigiria a instalação de um novo ficheiro de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: como número de chaves que pode ser suportado num HSM dedicados?

O número máximo de teclas é uma função da memória disponível. O Modelo A790 SafeNet Luna 7 em uso tem 32MB de memória. Os seguintes números também são aplicáveis aos pares-chave se utilizarem chaves assimétricas.

* RSA 2048 - 19,000
* ECC-P256 - 91,000

Capacidade irá variar de acordo com os atributos chave específicos definidos no modelo de geração de chaves e número de partições.
