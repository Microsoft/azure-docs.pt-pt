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
ms.date: 5/8/2019
ms.author: mbaldwin
ms.openlocfilehash: b79ed7ea3113f097f767ad7ff8bdc47b4e4916eb
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883559"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Encontre respostas a perguntas comuns sobre o HSM dedicados do Microsoft Azure.

## <a name="the-basics"></a>As noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P. O que é um HSM (módulo de segurança de hardware)?

Um módulo de segurança de Hardware (HSM) é um dispositivo de computação físico utilizado para salvaguardar e gerir chaves criptográficas. Chaves armazenadas em HSMs podem ser utilizadas para operações criptográficas. O material de chave com segurança permanece em módulos de hardware resistente a adulterações, adulterar-evidente. O HSM só permite autenticado e autorizado a aplicações para utilizar as chaves. O material das chaves nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P. O que é a oferta HSM dedicada do Azure?

HSM dedicada do Azure é um serviço baseado na nuvem que fornece HSMs alojados em datacenters do Azure que estejam diretamente ligados à rede virtual de um cliente. Estes HSMs são aplicações de rede dedicado (da Gemalto SafeNet rede HSM 7 modelo A790). Elas serem implantadas diretamente para o espaço de endereços IP privado uma parte dos clientes e a Microsoft não tem qualquer acesso à funcionalidade de criptografia dos HSMs. Apenas o cliente tem controle completo administrativa e criptografia sobre estes dispositivos. Os clientes são responsáveis pela gestão do dispositivo e podem obter registos de atividade completo diretamente a partir dos seus dispositivos. HSMs dedicados ajudam os clientes a cumprir os requisitos de conformidade regulamentar, como FIPS 140-2 nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P. Qual hardware é utilizado para HSM dedicados?

A Microsoft estabeleceu uma parceria com a Gemalto para fornecer o serviço HSM dedicado do Azure. O dispositivo específico utilizado é o [HSM de rede do SafeNet Luna 7 modelo A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Este dispositivo não apenas fornece FIPS 140-2 nível 3 validado firmware, mas também oferece baixa latência, elevado desempenho e elevada capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P. Para que são utilizados os HSMs?

Os HSMs são utilizados para armazenar chaves criptográficas que, por sua vez, são utilizadas para proporcionar funcionalidades criptográficas, como SSL (Secure Sockets Layer), encriptação de dados, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e assinar documentos.

### <a name="q-how-does-dedicated-hsm-work"></a>P. Como funciona o HSM Dedicado?

Os clientes podem aprovisionar HSMs em regiões específicas com o PowerShell ou a interface de linha de comandos. O cliente especifica qual rede virtual serão ligados ao e uma vez provisionados os HSMs os HSMs estarão disponíveis na sub-rede designada em endereços IP atribuídos no espaço de endereços IP privados do cliente. Em seguida, os clientes podem ligar-se para os HSMs através de SSH para a gestão da aplicação e administração, configurar ligações de cliente HSM, inicializar HSMs, criar partições, definir e atribuir funções como diretor de partição, diretor de criptografia e utilizador criptografia. Em seguida, o cliente irá utilizar Gemalto fornecida HSM ferramentas/SDK/software de cliente para efetuar operações de criptografia a partir das suas aplicações.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P. Qual software é fornecido com o serviço HSM dedicado?

Gemalto fornece todo o software para o dispositivo HSM aprovisionado uma vez pela Microsoft. O software está disponível na [portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante. O software de cliente suportada é a versão 7.2, que é compatível com o FIPS 140-2 nível 3 validado firmware versão 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P. O HSM dedicado do Azure oferece autenticação baseada em senha e em PED?

Neste momento, o HSM dedicado do Azure fornece apenas HSMs com autenticação baseada em senha.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P. O HSM dedicado do Azure vai hospedar meus HSMs para mim?

A Microsoft oferece apenas o Gemalto SafeNet Luna Network HSM por meio do serviço HSM dedicado e não pode hospedar nenhum dispositivo fornecido pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>P. O HSM dedicado do Azure dá suporte a recursos de pagamento (PIN/ETF)?

O serviço HSM dedicado do Azure usa dispositivos SafeNet Luna Network HSM 7 (Model A790). Esses dispositivos não dão suporte à funcionalidade específica do HSM de pagamento (como PIN ou ETF) ou certificações. Se você quiser que o serviço HSM dedicado do Azure ofereça suporte a HSMs de pagamento no futuro, passe os comentários para seu representante de conta da Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P. Em quais regiões do Azure o HSM dedicado está disponível?

Desde o final de março de 2019, o HSM dedicado está disponível nas 14 regiões listadas abaixo. Outras regiões são planejadas e podem ser discutidas por meio de seu representante de conta da Microsoft.

* East US
* EUA Leste 2
* EUA Oeste
* EUA Centro-Sul
* Sudeste Asiático
* Ásia Oriental
* Europa do Norte
* Europa Ocidental
* Reino Unido Sul
* Reino Unido Oeste
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P. Como meu aplicativo se conecta a um HSM dedicado?

Utilize Gemalto fornecida HSM ferramentas/SDK/software de cliente para realizar operações criptográficas das suas aplicações. O software está disponível na [portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P. Um aplicativo pode se conectar ao HSM dedicado de uma VNET diferente em ou entre regiões?

Sim, precisará usar [VNET peering](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer a conectividade entre, redes virtuais. Para a conectividade entre regiões, tem de utilizar [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P. Posso sincronizar o HSM dedicado com HSMs locais?

Sim, pode sincronizar HSMs no local com o HSM dedicados. [Ponto a ponto VPN ou ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md) conectividade pode ser utilizada para estabelecer conectividade com a sua rede no local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P. Posso criptografar dados usados por outros serviços do Azure usando chaves armazenadas em HSM dedicado?

Não. São apenas acessíveis a partir de dentro da sua rede virtual do Azure de HSMs dedicados.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P. Posso importar chaves de um HSM local existente para HSM dedicado?

Sim, se tiver no local da Gemalto SafeNet HSMs. Existem vários métodos. Consulte a documentação da Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P. Quais sistemas operacionais são compatíveis com o software cliente HSM dedicado?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* VirtuaisLUNs VMware, HyperV, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P. Como fazer configurar meu aplicativo cliente para criar uma configuração de alta disponibilidade com várias partições de vários HSMs?

Para ter uma elevada disponibilidade, terá de definir a configuração de aplicação de cliente do HSM para utilizar partições cada HSM. Consulte a documentação de software de cliente da Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P. Quais mecanismos de autenticação têm suporte pelo HSM dedicado?

HSM dedicada do Azure utiliza aplicações de 7 de HSM de rede SafeNet (modelos A790) e que suportam autenticação baseada em palavra-passe.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P. Quais SDKs, APIs e software cliente estão disponíveis para uso com o HSM dedicado?

PKCS #11, o Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P. Posso importar/migrar chaves de um HSMs de 5/6 para HSMs dedicados do Azure?

Sim. Consulte o guia de migração da Gemalto. 

## <a name="using-your-hsm"></a>Utilizar o seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P. Como fazer decidir se deseja usar o Azure Key Vault ou o HSM dedicado do Azure?

HSM dedicada do Azure é a opção adequada para as empresas a migrar para aplicações do Azure no local que utilizam o HSMs. HSMs dedicados apresentam uma opção para migrar uma aplicação com alterações mínimas. Se as operações criptográficas são executadas no código do aplicativo em execução numa VM do Azure ou a aplicação Web, eles podem usar o HSM dedicados. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, por exemplo, o Gestor de gateway ou o tráfego de aplicações para SSL sem chave, o ADCS (Active Directory Certificate Services), ou ferramentas PKI similares, ferramentas/aplicações utilizadas para assinatura de documentos, a assinatura de código ou um SQL Server (IaaS) configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível). O Azure Key Vault é adequado para aplicações "born-in-cloud" ou para a encriptação em cenários de rest onde os dados dos clientes são processados por PaaS (plataforma como serviço) ou a cenários de SaaS (Software como serviço) como chave de cliente do Office 365, Azure Information Protection , O azure Disk Encryption, a encriptação do Azure Data Lake Store com encriptação principal, de armazenamento do Azure gerida pelo cliente com o cliente gerido chave e SQL do Azure com o cliente gerido chave.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P. Quais cenários de uso melhor se adaptam ao HSM dedicado do Azure?

HSM dedicada do Azure é mais adequado para cenários de migração. Isso significa que, se estiver a migrar aplicações no local para o Azure que já estão a utilizar HSMs. Isso fornece uma opção de baixa fricção para migrar para o Azure com alterações mínimas à aplicação. Se as operações criptográficas são executadas no código do aplicativo em execução na VM do Azure ou a aplicação Web, HSM dedicados podem ser utilizado. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, tais como:

* Gestor de tráfego ou gateway de aplicação para SSL sem chave
* ADCS (Serviços de certificados do Active Directory)
* Ferramentas similares de PKI
* Ferramentas/aplicações utilizadas para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS), configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P. O HSM Dedicado pode ser utilizado com a Chave de Cliente do Office 365, o Azure Information Protection, o Azure Data Lake Store, a Encriptação de Discos, a encriptação de Armazenamento do Azure e o TDE do SQL do Azure?

Não. HSM dedicados é aprovisionado diretamente no espaço de endereço IP privado de um cliente para que ele faz não está acessível por outros serviços do Azure ou a Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controlo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P. O cliente obtém total controle exclusivo sobre os HSMs com HSMs dedicados?

Sim. Cada aplicação de HSM está totalmente dedicada de um único cliente, e ninguém tem controle administrativo, uma vez aprovisionado e a palavra-passe de administrador foi alterado.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P. Que nível de acesso a Microsoft tem ao meu HSM?

Microsoft não tem qualquer controlo administrativo ou criptografia sobre o HSM. A Microsoft ter acesso de nível de monitor através de ligação de porta serial para obter telemetria básica, tais como o estado de funcionamento do componente e de temperatura. Isso permite que a Microsoft fornece notificação proativa de problemas de estado de funcionamento. Se for necessário, o cliente pode desativar esta conta.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P. O que é a conta "tenantadmin" que a Microsoft usa, estou acostumado com o usuário administrador "admin" em SafeNet HSMs?

O dispositivo HSM é fornecido com um usuário padrão administrador com sua senha padrão usual. A Microsoft não queria ter senhas padrão em uso enquanto qualquer dispositivo estiver em um pool aguardando para ser provisionado pelos clientes. Isso não atenderia aos nossos requisitos de segurança estritos. Por esse motivo, definimos uma senha forte que é descartada no momento do provisionamento. Além disso, no tempo de provisionamento, criamos um novo usuário na função de administrador chamado "tenantadmin". Esse usuário tem a senha padrão e os clientes alteram isso como a primeira ação ao fazer logon primeiro no dispositivo provisionado recentemente. Esse processo garante altos níveis de segurança e mantém nossa promessa de controle administrativo exclusivo para nossos clientes. Deve-se observar que o usuário "tenantadmin" pode ser usado para redefinir a senha de usuário do administrador se um cliente preferir usar essa conta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P. A Microsoft ou qualquer pessoa pode acessar chaves do Microsoft Access em meu HSM dedicado?

Não. Microsoft não tem qualquer acesso às chaves armazenadas no cliente alocado HSM dedicados.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P. Posso atualizar software/firmware em HSMs alocados para mim?

Para obter o melhor suporte, a Microsoft recomenda vivamente não fazer a atualização de software/firmware no HSM. No entanto, o cliente ter controle administrativo completo, incluindo a atualização de software/firmware se são necessárias de versões de firmware diferentes funcionalidades específicas. Antes de efetuar alterações, as implicações devem ser compreendidas como isso poderia, por exemplo, o efeito FIPS validado estado. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P. Como fazer gerenciar o HSM dedicado?

Pode gerir HSMs dedicados ao aceder às mesmas através de SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P. Como fazer gerenciar partições no HSM dedicado?

O software de cliente de Gemalto HSM é utilizado para gerir os HSMs e partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P. Como fazer monitorar meu HSM?

Um cliente tem acesso total aos registos de atividades HSM através do syslog e SNMP. Um cliente tem de configurar um servidor syslog ou um servidor SNMP para receber os registos ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P. Posso obter o log de acesso completo de todas as operações de HSM do HSM dedicado?

Sim. Pode enviar registos a partir da aplicação do HSM para um servidor syslog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P. É possível configurar a alta disponibilidade na mesma região ou em várias regiões?

Sim. Configuração de elevada disponibilidade e o programa de configuração são executadas no software de cliente HSM fornecido pelo Gemalto. Os HSMs da mesma rede virtual ou outros VNETs na mesma região ou entre regiões, ou HSMs locais conectados a uma VNET usando VPN site a site ou ponto a ponto podem ser adicionados à mesma configuração de alta disponibilidade. Deve-se observar que isso sincroniza somente o material da chave e não itens de configuração específicos, como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P. Posso adicionar HSMs de minha rede no local para um grupo de elevada disponibilidade com o HSM do Azure dedicado?

Sim. Têm de cumprir os requisitos de elevada disponibilidade para o 7 de HSM de rede de Luna SafeNet.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P. Posso adicionar Luna 5/6 HSMs de redes no local para um grupo de elevada disponibilidade com o HSM do Azure dedicado?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P. Quantos HSMs posso adicionar à mesma configuração de alta disponibilidade de um único aplicativo?

16 membros de um grupo de HA têm testes de aceleração total, com ótimos resultados.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P. Qual é o SLA para o serviço HSM dedicado?

Não há garantia de tempo de atividade específica fornecida para o serviço HSM dedicado. Microsoft irá garantir o acesso de nível de rede para o dispositivo e, por conseguinte, aplicam o padrão SLAs de rede do Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P. Como os HSMs usados no HSM dedicado do Azure são protegidos?

Os datacenters do Azure tem controlos de segurança física e procedimentos. Além disso ou HSMs dedicados estão alojados numa área de ainda mais o acesso restrito do Centro de dados. Essas áreas tem controlos de acesso físico adicionais e vigilância de câmera de vídeo para obter segurança adicional.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P. O que acontece se houver uma violação de segurança ou um evento de violação de hardware?

Serviço HSM dedicado utiliza aplicações de 7 de HSM de rede SafeNet. Estas aplicações suportam a deteção de adulteração física e lógica. Se já existe um evento de adulterações que os HSMs são zeroized automaticamente.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P. Como fazer garantir que as chaves em meus HSMs dedicados não sejam perdidas devido a erros ou a um ataque interno mal-intencionado?

É altamente recomendado a utilizar um dispositivo de cópia de segurança de HSM no local para efetuar cópia de segurança periódica normal dos HSMs para recuperação após desastre. Terá de utilizar uma ligação de VPN ponto a ponto ou site a site para uma estação de trabalho no local ligada a um dispositivo de cópia de segurança do HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P. Como fazer obter suporte para HSM dedicado?

O suporte é fornecido pela Microsoft e pelo Gemalto.  Se você tiver um problema com o acesso ao hardware ou à rede, gere uma solicitação de suporte com a Microsoft e, se tiver um problema com a configuração do HSM, o software e o desenvolvimento de aplicativos, gere uma solicitação de suporte com o Gemalto. Se você tiver um problema indeterminado, gere uma solicitação de suporte com a Microsoft e, em seguida, Gemalto poderá ser envolvido conforme necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P. Como fazer obter o software cliente, a documentação e o acesso às diretrizes de integração para o HSM SafeNet Luna 7?

Após o registro para o serviço, será fornecida uma ID de cliente Gemalto que permite o registro no portal de suporte ao cliente do Gemalto. Isso permitirá o acesso a todos os softwares e documentações, bem como a habilitação de solicitações de suporte diretamente com o Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P. Se houver uma vulnerabilidade de segurança encontrada e um patch for lançado pelo Gemalto, que é responsável por atualizar/aplicar patches no sistema operacional/firmware?

Microsoft não tem a capacidade de conexão para HSMs alocadas para os clientes. Os clientes tem de atualizar e aplicar um patch seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P. E se eu precisar reinicializar meu HSM?

O HSM tem uma opção de reinicialização de linha de comando. no entanto, estamos enfrentando problemas de reinicialização intermitente e, por esse motivo, é recomendado para a reinicialização mais segura que você gera uma solicitação de suporte com a Microsoft para que o dispositivo tenha reinicializado fisicamente. 

## <a name="cryptography-and-standards"></a>Padrões e criptografia

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P. É seguro armazenar chaves de criptografia para meus dados mais importantes no HSM dedicado?

Sim, o HSM dedicados Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P. Quais algoritmos e chaves de criptografia são compatíveis com o HSM dedicado?

Aplicações de provisões 7 de HSM de rede SafeNet do serviço HSM dedicadas. Eles dão suporte a uma ampla variedade de algoritmos e tipos de chave de criptografia, incluindo: Suporte completo a Suite B

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
  * Hash/Resumo de mensagem/HMAC: SHA-1, SHA-2, SM3
  * Derivação de chave: Modo de contador SP800-108
  * Quebra automática de chave: SP800-38F
  * Geração de número aleatório: FIPS 140-2 aprovou DRBG (modo SP 800-90 CTR), conformidade com BSI DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P. O HSM FIPS 140-2 nível 3 é validado?

Sim. Serviço HSM dedicado Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P. O que preciso fazer para ter certeza de que opere o HSM dedicado no modo validado do FIPS 140-2 nível 3?

O serviço de HSM dedicados Aprovisiona aparelhos de 7 de HSM de rede de Luna SafeNet. HSMs validadas por estes uso de aparelhos FIPS 140-2 nível 3. A configuração predefinida implementada, o sistema operativo e o firmware também são FIPS validada. Não é necessário efetuar qualquer ação de conformidade do FIPS 140-2 nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P. Como é que um cliente garantir a que quando um HSM é desaprovisionado todo o material de chave serem eliminado?

Antes de solicitar o desaprovisionamento, um cliente tem de ter a zeroized o HSM através da Gemalto fornecida ferramentas de cliente do HSM.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P. Quantas operações criptográficas têm suporte por segundo com o HSM dedicado?

Aplicações de provisões 7 de HSM de rede SafeNet HSM dedicadas (modelo A790). Aqui está um resumo de desempenho máximo para algumas operações: 

* RSA-2048: 10.000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P. Quantas partições podem ser criadas no HSM dedicado?

O modelo A790 SafeNet Luna HSM 7 usado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 partições e a adição de partições até esse limite incorrerá em custos de licenciamento extras e exigirá a instalação de um novo arquivo de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P. Quantas chaves podem ser suportadas no HSM dedicado?

O número máximo de chaves é uma função da memória disponível. O modelo SafeNet Luna 7 A790 em uso tem 32 MB de memória. Os números a seguir também se aplicam a pares de chaves se estiver usando chaves assimétricas.

* RSA 2048 - 19,000
* ECC-P256 - 91,000

Capacidade irá variar de acordo com os atributos chave específicos definidos no modelo de geração de chaves e número de partições.
