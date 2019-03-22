---
title: Perguntas mais frequentes sobre - HSM dedicada do Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre que aborda tópicos diferentes no HSM dedicada do Azure
services: dedicated-hsm
author: johndaw
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/19/2019
ms.author: barclayn
ms.openlocfilehash: b28d9607bf35d37e252d7d0bc59d1ce808e38665
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259997"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

Encontre respostas a perguntas comuns sobre o HSM dedicados do Microsoft Azure.

## <a name="the-basics"></a>As noções básicas

### <a name="q-what-is-a-hardware-security-module-hsm"></a>P: O que é um módulo de segurança de hardware (HSM)?

Um módulo de segurança de Hardware (HSM) é um dispositivo de computação físico utilizado para salvaguardar e gerir chaves criptográficas. Chaves armazenadas em HSMs podem ser utilizadas para operações criptográficas. O material de chave com segurança permanece em módulos de hardware resistente a adulterações, adulterar-evidente. O HSM só permite autenticado e autorizado a aplicações para utilizar as chaves. O material das chaves nunca sai do limite de proteção do HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: O que é a oferta de HSM dedicados do Azure?

HSM dedicada do Azure é um serviço baseado na nuvem que fornece HSMs alojados em datacenters do Azure que estejam diretamente ligados à rede virtual de um cliente. Estes HSMs são aplicações de rede dedicado (da Gemalto SafeNet rede HSM 7 modelo A790). Elas serem implantadas diretamente para o espaço de endereços IP privado uma parte dos clientes e a Microsoft não tem qualquer acesso à funcionalidade de criptografia dos HSMs. Apenas o cliente tem controle completo administrativa e criptografia sobre estes dispositivos. Os clientes são responsáveis pela gestão do dispositivo e podem obter registos de atividade completo diretamente a partir dos seus dispositivos. HSMs dedicados ajudam os clientes a cumprir os requisitos de conformidade regulamentar, como FIPS 140-2 nível 3, HIPAA, PCI-DSS e eIDAS e muitos outros.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: Qual hardware é utilizado para HSM dedicados?

Microsoft associou-se a Gemalto para fornecer o serviço HSM de dedicada do Azure. O dispositivo específico utilizado é o [HSM de rede do SafeNet Luna 7 modelo A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Este dispositivo não apenas fornece FIPS 140-2 nível 3 validado firmware, mas também oferece baixa latência, elevado desempenho e elevada capacidade por meio de 10 partições. 

### <a name="q-what-is-an-hsm-used-for"></a>P: Para que são utilizados os HSMs?

Os HSMs são utilizados para armazenar chaves criptográficas que, por sua vez, são utilizadas para proporcionar funcionalidades criptográficas, como SSL (Secure Sockets Layer), encriptação de dados, PKI (infraestrutura de chaves públicas), DRM (gestão de direitos digitais) e assinar documentos.

### <a name="q-how-does-dedicated-hsm-work"></a>P: Como funciona o HSM Dedicado?

Os clientes podem aprovisionar HSMs em regiões específicas com o PowerShell ou a interface de linha de comandos. O cliente especifica qual rede virtual serão ligados ao e uma vez provisionados os HSMs os HSMs estarão disponíveis na sub-rede designada em endereços IP atribuídos no espaço de endereços IP privados do cliente. Em seguida, os clientes podem ligar-se para os HSMs através de SSH para a gestão da aplicação e administração, configurar ligações de cliente HSM, inicializar HSMs, criar partições, definir e atribuir funções como diretor de partição, diretor de criptografia e utilizador criptografia. Em seguida, o cliente irá utilizar Gemalto fornecida HSM ferramentas/SDK/software de cliente para efetuar operações de criptografia a partir das suas aplicações.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: Qual software é fornecido com o serviço de HSM dedicados?

Gemalto fornece todo o software para o dispositivo HSM aprovisionado uma vez pela Microsoft. O software está disponível na [portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante. O software de cliente suportada é a versão 7.2, que é compatível com o FIPS 140-2 nível 3 validado firmware versão 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: O HSM do Azure dedicado oferece autenticação baseados em palavra-passe e PED?

Neste momento, o HSM do Azure dedicado fornece apenas HSMs com autenticação baseada em palavra-passe.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: HSM do Azure dedicado hospedará meu HSMs para mim?

A Microsoft apenas oferece o HSM de rede do Gemalto SafeNet Luna através do serviço de HSM dedicados e não é possível alojar quaisquer dispositivos fornecidos pelo cliente.

### <a name="q-does-azure-dedicated-hsm-support-pinetf-features-or-meet-pci-hsm-v1-or-v2-certification-requirements"></a>P: HSM dedicada do Azure suportam funcionalidades PIN/ETF ou cumprir requisitos de certificação de HSM de PCI v1 ou v2?

O serviço HSM de dedicada do Azure utiliza dispositivos SafeNet Luna rede HSM 7 (modelo A790). Estes dispositivos não suportam a funcionalidade específica do HSM de pagamento (por exemplo, PIN ou ETF) ou certificações. Se desejar que o serviço HSM de dedicada do Azure para suportar o pagamento HSMs no futuro, introduza os comentários abaixo.

## <a name="interoperability"></a>Interoperabilidade

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: Como é que meu aplicativo ligar-se um HSM dedicados?

Utilize Gemalto fornecida HSM ferramentas/SDK/software de cliente para realizar operações criptográficas das suas aplicações. O software está disponível na [portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/). Clientes que utilizam o serviço de HSM dedicados têm de ser registado para Gemalto suporte e de ter um ID de cliente que permite o acesso e a transferência de software relevante.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: Pode um aplicativo ligar ao HSM dedicados a partir de uma VNET diferente num ou em regiões?

Sim, precisará usar [VNET peering](../virtual-network/virtual-network-peering-overview.md) dentro de uma região para estabelecer a conectividade entre, redes virtuais. Para a conectividade entre regiões, tem de utilizar [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: Pode sincronizar HSM dedicados com HSMs no local?

Sim, pode sincronizar HSMs no local com o HSM dedicados. [Ponto a ponto VPN ou ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md) conectividade pode ser utilizada para estabelecer conectividade com a sua rede no local.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: Pode encriptar dados utilizados por outros serviços do Azure utilizando chaves armazenadas em HSM dedicados?

Não. São apenas acessíveis a partir de dentro da sua rede virtual do Azure de HSMs dedicados.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: Posso importar chaves a partir de um HSM no local existente para o HSM dedicados?

Sim, se tiver no local da Gemalto SafeNet HSMs. Existem vários métodos. Consulte a documentação da Gemalto HSM.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: Que sistemas operativos são suportados pelo software de cliente de HSM dedicados?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>P: Como posso configurar a minha aplicação de cliente para criar uma configuração de elevada disponibilidade com várias partições a partir dos vários HSMs?

Para ter uma elevada disponibilidade, terá de definir a configuração de aplicação de cliente do HSM para utilizar partições cada HSM. Consulte a documentação de software de cliente da Gemalto HSM.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: Que mecanismos de autenticação são suportados por HSM dedicados?

HSM dedicada do Azure utiliza aplicações de 7 de HSM de rede SafeNet (modelos A790) e que suportam autenticação baseada em palavra-passe.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: Que software de cliente de SDKs, APIs, está disponível para utilização com o HSM dedicados?

PKCS #11, o Java (JCA/JCE), Microsoft CAPI e CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: Posso importar/migrar as chaves de Luna 5/6 HSMs para HSMs do Azure dedicado?

Sim. Consulte o guia de migração da Gemalto. 

## <a name="using-your-hsm"></a>Utilizar o seu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Como decidir se deve utilizar o Azure Key Vault ou o HSM do Azure dedicado?

HSM dedicada do Azure é a opção adequada para as empresas a migrar para aplicações do Azure no local que utilizam o HSMs. HSMs dedicados apresentam uma opção para migrar uma aplicação com alterações mínimas. Se as operações criptográficas são executadas no código do aplicativo em execução numa VM do Azure ou a aplicação Web, eles podem usar o HSM dedicados. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, por exemplo, o Gestor de gateway ou o tráfego de aplicações para SSL sem chave, o ADCS (Active Directory Certificate Services), ou ferramentas PKI similares, ferramentas/aplicações utilizadas para assinatura de documentos, a assinatura de código ou um SQL Server (IaaS) configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível). O Azure Key Vault é adequado para aplicações "born-in-cloud" ou para a encriptação em cenários de rest onde os dados dos clientes são processados por PaaS (plataforma como serviço) ou a cenários de SaaS (Software como serviço) como chave de cliente do Office 365, Azure Information Protection , O azure Disk Encryption, a encriptação do Azure Data Lake Store com encriptação principal, de armazenamento do Azure gerida pelo cliente com o cliente gerido chave e SQL do Azure com o cliente gerido chave.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: Quais cenários de utilização que melhor se adequar às HSM do Azure dedicado?

HSM dedicada do Azure é mais adequado para cenários de migração. Isso significa que, se estiver a migrar aplicações no local para o Azure que já estão a utilizar HSMs. Isso fornece uma opção de baixa fricção para migrar para o Azure com alterações mínimas à aplicação. Se as operações criptográficas são executadas no código do aplicativo em execução na VM do Azure ou a aplicação Web, HSM dedicados podem ser utilizado. Em geral, compactado software em execução nos modelos de IaaS (infraestrutura como serviço), que suportam HSMs como um arquivo de chaves pode usar o HSM dedicar, tais como:

* Gestor de tráfego ou gateway de aplicação para SSL sem chave
* ADCS (Serviços de certificados do Active Directory)
* Ferramentas similares de PKI
* Ferramentas/aplicações utilizadas para assinatura de documentos
* Assinatura de código
* SQL Server (IaaS), configurado com TDE (encriptação de base de dados transparente) com a chave mestra de um HSM através de um fornecedor do EKM (gerenciamento de chaves extensível)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: O HSM Dedicado pode ser utilizado com a Chave de Cliente do Office 365, o Azure Information Protection, o Azure Data Lake Store, a Encriptação de Discos, a encriptação de Armazenamento do Azure e o TDE do SQL do Azure?

Não. HSM dedicados é aprovisionado diretamente no espaço de endereço IP privado de um cliente para que ele faz não está acessível por outros serviços do Azure ou a Microsoft.

## <a name="administration-access-and-control"></a>Administração, acesso e controlo

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>P: O cliente obter o controlo completo exclusivo sobre os HSMs com HSMs dedicados?

Sim. Cada aplicação de HSM está totalmente dedicada de um único cliente, e ninguém tem controle administrativo, uma vez aprovisionado e a palavra-passe de administrador foi alterado.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: O nível de acesso a Microsoft tem para o meu HSM?

Microsoft não tem qualquer controlo administrativo ou criptografia sobre o HSM. A Microsoft ter acesso de nível de monitor através de ligação de porta serial para obter telemetria básica, tais como o estado de funcionamento do componente e de temperatura. Isso permite que a Microsoft fornece notificação proativa de problemas de estado de funcionamento. Se for necessário, o cliente pode desativar esta conta.

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: Pode a Microsoft ou para alguém da chaves de acesso da Microsoft no meu HSM dedicados?

Não. Microsoft não tem qualquer acesso às chaves armazenadas no cliente alocado HSM dedicados.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: Posso atualizar o software/firmware nos HSMs alocado para mim?

Para obter o melhor suporte, a Microsoft recomenda vivamente não fazer a atualização de software/firmware no HSM. No entanto, o cliente ter controle administrativo completo, incluindo a atualização de software/firmware se são necessárias de versões de firmware diferentes funcionalidades específicas. Antes de efetuar alterações, as implicações devem ser compreendidas como isso poderia, por exemplo, o efeito FIPS validado estado. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: Como posso gerir HSM dedicados?

Pode gerir HSMs dedicados ao aceder às mesmas através de SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: Como posso gerir partições no HSM dedicados?

O software de cliente de Gemalto HSM é utilizado para gerir os HSMs e partições.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: Como posso monitorizar o meu HSM?

Um cliente tem acesso total aos registos de atividades HSM através do syslog e SNMP. Um cliente tem de configurar um servidor syslog ou um servidor SNMP para receber os registos ou eventos dos HSMs.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: Posso obter o registo de acesso total de todas as operações de HSM de HSM dedicados?

Sim. Pode enviar registos a partir da aplicação do HSM para um servidor syslog

## <a name="high-availability"></a>Elevada disponibilidade

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: É possível configurar a elevada disponibilidade na mesma região ou em várias regiões?

Sim. Configuração de elevada disponibilidade e o programa de configuração são executadas no software de cliente HSM fornecido pelo Gemalto. HSMs da mesma VNET ou de outras VNETs na mesma região ou em várias regiões ou no local HSMs ligado a uma VNET com o site a site ou VPN de ponto a ponto pode ser adicionado a mesma configuração de elevada disponibilidade. É importante observar que sincroniza apenas a material de chave e itens de configuração não específico, como funções.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar HSMs de minha rede no local para um grupo de elevada disponibilidade com o HSM do Azure dedicado?

Sim. Têm de cumprir os requisitos de elevada disponibilidade para o 7 de HSM de rede de Luna SafeNet.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Posso adicionar Luna 5/6 HSMs de redes no local para um grupo de elevada disponibilidade com o HSM do Azure dedicado?

Não.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: HSMs quantos posso adicionar para a mesma configuração de elevada disponibilidade de um único aplicativo?

16 membros de um grupo HA tem em foram, Acelerador completo de teste com resultados excelentes.

## <a name="support"></a>Suporte

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: O que é o SLA para o serviço HSM dedicados?

Não há nenhum guarentee de tempo de atividade específica fornecido para o serviço de HSM dedicados. Microsoft irá garantir o acesso de nível de rede para o dispositivo e, por conseguinte, aplicam o padrão SLAs de rede do Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: Como os HSMs são usados no Azure dedicado protegidas por HSM?

Os datacenters do Azure tem controlos de segurança física e procedimentos. Além disso ou HSMs dedicados estão alojados numa área de ainda mais o acesso restrito do Centro de dados. Essas áreas tem controlos de acesso físico adicionais e vigilância de câmera de vídeo para obter segurança adicional.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: O que acontece se ocorrer uma violação de segurança ou do hardware adulteração eventos?

Serviço HSM dedicado utiliza aplicações de 7 de HSM de rede SafeNet. Estas aplicações suportam a deteção de adulteração física e lógica. Se já existe um evento de adulterações que os HSMs são zeroized automaticamente.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: Como garantir que as chaves na minha HSMs dedicado não são perdidas devido a erro ou um ataque insider malicioso?

É altamente recomendado a utilizar um dispositivo de cópia de segurança de HSM no local para efetuar cópia de segurança periódica normal dos HSMs para recuperação após desastre. Terá de utilizar uma ligação de VPN ponto a ponto ou site a site para uma estação de trabalho no local ligada a um dispositivo de cópia de segurança do HSM.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: Como posso obter suporte para o HSM dedicados?

O suporte é fornecido pela Microsoft e da Gemalto.  Se tiver um problema com o hardware ou acesso de rede, faça um pedido de suporte com a Microsoft e caso tenha um problema com o desenvolvimento de aplicações, software e configuração de HSM entre rasie pedido de suporte com Gemalto. Se tiver um problema indeterminado, emitir um withg de pedido de suporte Microsoft e, em seguida, Gemalto pode ser combinada como necessário. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Como obtenho o cliente de software, documentação e acesso a orientação de integração para o HSM do SafeNet Luna 7?

Depois de se registrar para o serviço, um ID de cliente da Gemalto será fornecida permitindo que para o registo no portal de suporte de cliente do Gemalto. Isto irá permitir o acesso a todos os software e documentação, bem como ativar pedidos de suporte diretamente com a Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>P: Se houver uma vulnerabilidade de segurança encontrada e um patch é lançado pela Gemalto, que é responsável pela aplicação de patches e atualizar o sistema operacional/Firmware?

Microsoft não tem a capacidade de conexão para HSMs alocadas para os clientes. Os clientes tem de atualizar e aplicar um patch seus HSMs.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: E se eu precisar de reiniciar a minha HSM?

 O HSM tem uma opção de reinício de linha de comandos e é importante observar que a opção de "reinício" tem de ser utilizada. Se isso falhar por algum motivo, faça um pedido de suporte com a Microsoft e podemos ter o dispositivo fisicamente reiniciado. 

## <a name="cryptography-and-standards"></a>Padrões e criptografia

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: É seguro armazenar chaves de encriptação com os meus dados mais importantes num HSM dedicados?

Sim, o HSM dedicados Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: As chaves criptográficas e os algoritmos são suportados por HSM dedicados?

Aplicações de provisões 7 de HSM de rede SafeNet do serviço HSM dedicadas. Eles oferecem suporte uma ampla variedade de tipos de chaves criptográficos e algoritmos, incluindo: Suporte total do Suite B

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
  * Hash/mensagem de texto implícita/HMAC: SHA-1, SHA-2, SM3
  * Derivação de chaves: Modo de contador de SP800 108
  * Encapsulamento principais: SP800-38F
  * Geração de números aleatórios: O FIPS 140-2 aprovado DRBG (modo de SP 800 90 CTR), o cumprimento BSI DRG.4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: É dedicado HSM FIPS 140-2 nível 3 validada?

Sim. Serviço HSM dedicado Aprovisiona os aparelhos de 7 de HSM de rede SafeNet que HSMs validadas pela FIPS 140-2 nível 3 de utilização.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: O que preciso fazer para se certificar de que eu operar HSM dedicados no FIPS 140-2 nível 3 validado modo?

O serviço de HSM dedicados Aprovisiona aparelhos de 7 de HSM de rede de Luna SafeNet. HSMs validadas por estes uso de aparelhos FIPS 140-2 nível 3. A configuração predefinida implementada, o sistema operativo e o firmware também são FIPS validada. Não é necessário efetuar qualquer ação de conformidade do FIPS 140-2 nível 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: Como é que um cliente garantir a que quando um HSM é desaprovisionado todo o material de chave serem eliminado?

Antes de solicitar o desaprovisionamento, um cliente tem de ter a zeroized o HSM através da Gemalto fornecida ferramentas de cliente do HSM.

## <a name="performance-and-scale"></a>Desempenho e dimensionamento

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: Quantas operações criptográficas são suportadas por segundo com o HSM dedicados?

Aplicações de provisões 7 de HSM de rede SafeNet HSM dedicadas (modelo A790). Aqui está um resumo de desempenho máximo para algumas operações: 

* RSA-2048: 10 000 transações por segundo
* ECC P256: 20.000 transações por segundo
* AES-GCM: 17.000 transações por segundo

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: Quantas partições podem ser criadas no HSM dedicados?

O modelo de SafeNet Luna HSM 7 que a790 utilizado inclui uma licença para 10 partições no custo do serviço. O dispositivo tem um limite de 100 partições e adicionar partições até esse limite poderia incorrer em custos de licenciamento extra e exigir a instalação de um novo ficheiro de licença no dispositivo.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: Suporte ao número de chaves HSM dedicados?

O número máximo de chaves é uma função de memória disponível. O modelo SafeNet Luna 7 A790 utilizado tem 32MB de memória. Os números a seguir também são aplicáveis aos pares de chaves, se utilizar as chaves assimétricas.

* RSA 2048 - 19,000
* ECC-P256 - 91,000

Capacidade irá variar de acordo com os atributos chave específicos definidos no modelo de geração de chaves e número de partições.
