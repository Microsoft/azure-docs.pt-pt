---
title: Recursos de segurança que podem ser utilizados com o armazenamento do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral das principais funcionalidades de segurança do Azure que podem ser utilizadas com o armazenamento do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 74c24e05d974c4b3bb15a242185b645231f74580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792773"
---
# <a name="azure-storage-security-overview"></a>Descrição geral da segurança de armazenamento do Azure

Este artigo fornece uma descrição geral das funcionalidades de segurança do Azure que pode utilizar com o armazenamento do Azure. O Azure Storage é a solução de armazenamento na cloud para aplicações modernas que dependem da durabilidade, da disponibilidade e da escalabilidade para satisfazer as necessidades dos seus clientes. Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança. Pode:

* Proteger a conta de armazenamento utilizando o controlo de acesso baseado em funções (RBAC) e o Azure Active Directory.
* Proteger dados em trânsito entre um aplicativo e o Azure através de encriptação do lado do cliente, HTTPS ou SMB 3.0.
* Definir os dados sejam encriptados automaticamente quando é escrita no armazenamento do Azure através de encriptação do serviço de armazenamento.
* Conjunto de dados e SO discos utilizados por máquinas virtuais (VMs) sejam encriptados ao utilizar o Azure Disk Encryption.
* Conceda acesso delegado para os objetos de dados no armazenamento do Azure ao utilizar assinaturas de acesso partilhado (SASs).
* Utilize a análise para controlar o método de autenticação que alguém está a utilizar quando acedem de armazenamento.

Para uma visão mais detalhada de segurança no armazenamento do Azure, consulte a [guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md). Este guia fornece uma descrição aprofundada os recursos de segurança do armazenamento do Azure. Estas funcionalidades incluem as chaves de conta de armazenamento, encriptação de dados em trânsito e em repouso e análise de armazenamento.

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções

Pode ajudar a proteger a sua conta de armazenamento utilizando o controlo de acesso baseado em funções. Restringir o acesso com base na [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados. Estes direitos de acesso são concedidos ao atribuir a função RBAC adequada a grupos e aplicações num determinado âmbito. Pode usar [funções RBAC incorporadas](../role-based-access-control/built-in-roles.md), tais como contribuinte de conta de armazenamento, para atribuir privilégios a utilizadores.

Saiba mais:

* [Controlo de acesso baseado em funções do Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado a objetos de armazenamento

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. A SAS significa que pode conceder a que um cliente permissões limitadas a objetos na conta de armazenamento durante um período especificado e com um conjunto especificado de permissões. Pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta.

A SAS é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa de fornecer a SAS para o método ou construtor apropriado.

Saiba mais:

* [Compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Criar e utilizar uma SAS com armazenamento de BLOBs](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

## <a name="encryption-in-transit"></a>Encriptação em trânsito

Encriptação em trânsito é um mecanismo de proteção de dados quando ele é transmitido através de redes. Com o armazenamento do Azure, pode proteger dados através de:

* [Encriptação de nível de transporte](../storage/common/storage-security-guide.md#encryption-in-transit), por exemplo, HTTPS, quando a transferência de dados dentro ou fora do armazenamento do Azure.
* [Ativar encriptação](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), tal como encriptação SMB 3.0, para o Azure partilhas de ficheiros.
* [Encriptação do lado do cliente](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para encriptar os dados antes de é transferido para o armazenamento e para descriptografar os dados depois de serem transferido para fora do armazenamento.

Saiba mais sobre a encriptação do lado do cliente:

* [Encriptação do lado do cliente do armazenamento do Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Série de controles de segurança de cloud: Encriptação de dados em trânsito](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Encriptação inativa

Para muitas organizações, [encriptação de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório no sentido de soberania de dados, conformidade e privacidade de dados. Três recursos do Azure fornecem criptografia de dados que estão inativos:

* [Encriptação do serviço de armazenamento](../storage/common/storage-security-guide.md#encryption-at-rest) está sempre ativada e encripta automaticamente dados de serviço de armazenamento ao escrevê-lo ao armazenamento do Azure.
* [Encriptação do lado do cliente](../storage/common/storage-security-guide.md#client-side-encryption) também fornece a funcionalidade de encriptação em repouso.
* [O Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite encriptar os discos de SO e discos de dados que utiliza a máquina virtual IaaS.

Saiba mais sobre a encriptação do serviço de armazenamento:

* [Encriptação do serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre os outros tipos de armazenamento do Azure, consulte [ficheiros do Azure](https://azure.microsoft.com/services/storage/files/), [armazenamento de tabelas](https://azure.microsoft.com/services/storage/tables/), e [armazenamento de filas](https://azure.microsoft.com/services/storage/queues/).
* [Encriptação do serviço de armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

O Azure Disk Encryption para máquinas virtuais ajuda-o a segurança organizacionais e requisitos de conformidade. Esta encripta os discos da VM (incluindo os discos de dados e de arranque) através da utilização de chaves e políticas controladas na [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Encriptação de disco para VMs funciona para sistemas operativos Linux e Windows. Também utiliza o Key Vault para o ajudar a salvaguardar, gerir e auditar a utilização das suas chaves de encriptação de disco. Todos os dados em seus discos VM são encriptados em descanso através da tecnologia de encriptação de norma da indústria nas suas contas de armazenamento do Azure. A solução de encriptação de disco para o Windows baseia [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), e a solução do Linux é baseada em [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais

* [Azure Disk Encryption para Windows e máquinas virtuais Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais

O armazenamento do Azure permite-lhe ativar as regras de firewall para as suas contas de armazenamento. Uma vez ativado, que irão bloquear os pedidos recebidos para os dados, incluindo pedidos de outros serviços do Azure. Pode configurar exceções para permitir o tráfego. Regras de firewall podem ser ativadas em contas de armazenamento existentes ou durante a hora de criação.

Deve usar essa funcionalidade para proteger as contas de armazenamento para um conjunto específico de redes permitidas.

Para obter mais informações sobre o armazenamento do Azure firewalls e redes virtuais, consulte o artigo [configurar Firewalls de armazenamento do Azure e redes virtuais](../storage/common/storage-network-security.md)

## <a name="azure-data-box"></a>Azure Data Box

Os dispositivos Data Box, Data Box Disk e Data Box Heavy ajudam a transferir grandes volumes de dados para o Azure quando a rede não é uma opção. Estes dispositivos de transferência de dados offline são entregues entre a sua organização e o Centro de dados do Azure. Utilizam encriptação AES para ajudar a proteger os dados em trânsito e são submetidos a um processo de limpeza pós-carregamento para eliminar dados do dispositivo.

Data Box Edge e Data Box Gateway são produtos de transferência de dados online que funcionam como gateways de armazenamento de rede para gerir dados entre o site e o Azure. O Data Box Edge, um dispositivo de rede no local, transfere dados de e para o Azure e utiliza computação edge ativada por inteligência artificial (AI) para processar dados. O Data Box Gateway é uma aplicação virtual com capacidades de gateway de armazenamento.

Saiba mais:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Edge de caixa de dados do Azure](../databox-online/data-box-edge-overview.md)
* [Gateway de caixa de dados do Azure](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Proteção Avançada Contra Ameaças

O armazenamento do Azure fornece proteção avançada contra ameaças para uma camada adicional de inteligência de segurança Deteta tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de sua conta de armazenamento. Avançado monitores de proteção contra ameaças registos de diagnóstico de armazenamento do Azure para leitura suspeita, escrever ou eliminar pedidos ao armazenamento de Blobs.

Alertas de proteção contra ameaças avançadas podem ser visualizadas a partir [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/). Centro de segurança do Azure fornece detalhes sobre qualquer atividade suspeita detetada e recomendando ações para investigar e remediar a ameaça potencial.

Saiba mais:

* [Armazenamento do Azure Advanced descrição geral da proteção de ameaças](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Utiliza o Azure Disk Encryption [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para ajudar a controlar e gerir chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. Também garante que todos os dados nos discos da máquina virtual são encriptados em inatividade no armazenamento do Azure. Deve utilizar o Cofre de chaves para chaves e a utilização da política de auditoria.

Saiba mais

* [O que é o Azure Key Vault?](../key-vault/key-vault-overview.md)
