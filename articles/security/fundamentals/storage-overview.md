---
title: Recursos de segurança que podem ser usados com o armazenamento do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos principais recursos de segurança do Azure que podem ser usados com o armazenamento do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: e273d913d07d8d7b77289695a4a28a478a64123a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350298"
---
# <a name="azure-storage-security-overview"></a>Visão geral de segurança do armazenamento do Azure

Este artigo fornece uma visão geral dos recursos de segurança do Azure que você pode usar com o armazenamento do Azure. O Azure Storage é a solução de armazenamento na cloud para aplicações modernas que dependem da durabilidade, da disponibilidade e da escalabilidade para satisfazer as necessidades dos seus clientes. O armazenamento do Azure fornece um conjunto abrangente de recursos de segurança. Pode:

* Proteja a conta de armazenamento usando o RBAC (controle de acesso baseado em função) e Azure Active Directory.
* Proteger os dados em trânsito entre uma aplicação e o Azure com a encriptação no lado do cliente, HTTPS ou SMB 3.0.
* Defina os dados a serem criptografados automaticamente quando gravados no armazenamento do Azure usando Criptografia do Serviço de Armazenamento.
* Defina o sistema operacional e OS discos de dados usados pelas VMs (máquinas virtuais) a serem criptografadas usando Azure Disk Encryption.
* Conceda acesso delegado aos objetos de dados no armazenamento do Azure usando as assinaturas de acesso compartilhado (SASs).
* Use a análise para acompanhar o método de autenticação que alguém está usando ao acessar o armazenamento.

Para obter uma visão mais detalhada da segurança no armazenamento do Azure, consulte o [Guia de segurança do armazenamento do Azure](../../storage/common/storage-security-guide.md). Este guia fornece uma análise aprofundada dos recursos de segurança do armazenamento do Azure. Esses recursos incluem chaves de conta de armazenamento, criptografia de dados em trânsito e em repouso e análise de armazenamento.

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções

Você pode ajudar a proteger sua conta de armazenamento usando o controle de acesso baseado em função. Restringir o acesso com base na [necessidade de conhecer](https://en.wikipedia.org/wiki/Need_to_know) e aos princípios de segurança de [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é imperativo para organizações que desejam impor políticas de segurança para acesso a dados. Esses direitos de acesso são concedidos atribuindo a função RBAC apropriada a grupos e aplicativos em um determinado escopo. Você pode usar [funções RBAC internas](/azure/role-based-access-control/built-in-roles), como colaborador da conta de armazenamento, para atribuir privilégios aos usuários.

Saiba mais:

* [Controle de acesso baseado em função Azure Active Directory](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado a objetos de armazenamento

Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. A SAS significa que você pode conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por um período especificado e com um conjunto especificado de permissões. Você pode conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

A SAS é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar os recursos de armazenamento com a SAS, o cliente precisa apenas fornecer a SAS para o construtor ou método apropriado.

Saiba mais:

* [Noções básicas sobre o modelo SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Criar e usar uma SAS com o armazenamento de BLOBs](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Criptografia em trânsito

A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o armazenamento do Azure, você pode proteger dados usando:

* [Criptografia no nível de transporte](/azure/storage/common/storage-security-guide#encryption-in-transit), como https, quando você transfere dados para dentro ou fora do armazenamento do Azure.
* [Criptografia de transmissão](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), como a criptografia SMB 3,0, para compartilhamentos de arquivos do Azure.
* [Criptografia do lado do cliente](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografar os dados após serem transferidos para fora do armazenamento.

Saiba mais sobre a criptografia do lado do cliente:

* [Criptografia do lado do cliente para Armazenamento do Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* série de controles de segurança [Cloud: Criptografando dados em trânsito @ no__t-0

## <a name="encryption-at-rest"></a>Encriptação inativa

Para muitas organizações, [a criptografia de dados em repouso](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória para a privacidade de dados, a conformidade e a soberania de dados. Três recursos do Azure fornecem criptografia de dados em repouso:

* [Criptografia do serviço de armazenamento](/azure/storage/common/storage-security-guide#encryption-at-rest) está sempre habilitado e criptografa automaticamente os dados do serviço de armazenamento ao gravá-los no armazenamento do Azure.
* A [criptografia do lado do cliente](/azure/storage/common/storage-security-guide#client-side-encryption) também fornece o recurso de criptografia em repouso.
* [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite criptografar os discos do sistema operacional e os discos de dados que uma máquina virtual IaaS usa.

Saiba mais sobre o Criptografia do Serviço de Armazenamento:

* O [azure criptografia do serviço de armazenamento](https://azure.microsoft.com/services/storage/) está disponível para o [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, consulte [arquivos do Azure](https://azure.microsoft.com/services/storage/files/), [armazenamento de tabelas](https://azure.microsoft.com/services/storage/tables/)e [armazenamento de filas](https://azure.microsoft.com/services/storage/queues/).
* [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption para máquinas virtuais ajuda você a atender aos requisitos de conformidade e segurança organizacional. Ele criptografa os discos de VM (incluindo discos de inicialização e de dados) usando as chaves e políticas que você controla no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A criptografia de disco para VMs funciona para sistemas operacionais Linux e Windows. Ele também usa Key Vault para ajudá-lo a proteger, gerenciar e auditar o uso de suas chaves de criptografia de disco. Todos os dados em seus discos de VM são criptografados em repouso usando a tecnologia de criptografia padrão da indústria em suas contas de armazenamento do Azure. A solução de criptografia de disco para Windows é baseada no [Microsoft criptografia de unidade de disco BitLocker](https://technet.microsoft.com/library/cc732774.aspx), e a solução Linux é baseada em [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt).

Obter mais informações

* [Visão geral de Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)

## <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais

O armazenamento do Azure permite que você habilite regras de firewall para suas contas de armazenamento. Uma vez habilitada, elas bloquearão solicitações de entrada de dados, incluindo solicitações de outros serviços do Azure. Você pode configurar exceções para permitir o tráfego. As regras de firewall podem ser habilitadas em contas de armazenamento existentes ou durante o tempo de criação.

Você deve usar essa funcionalidade para proteger suas contas de armazenamento para um conjunto específico de redes permitidas.

Para obter mais informações sobre firewalls de armazenamento do Azure e redes virtuais, confira o artigo [configurar redes virtuais e firewalls de armazenamento do Azure](/azure/storage/common/storage-network-security)

## <a name="azure-data-box"></a>Azure Data Box

Os dispositivos Data Box, Data Box Disk e Data Box Heavy ajudam a transferir grandes volumes de dados para o Azure quando a rede não é uma opção. Esses dispositivos de transferência de dados offline são enviados entre sua organização e o data center do Azure. Utilizam encriptação AES para ajudar a proteger os dados em trânsito e são submetidos a um processo de limpeza pós-carregamento para eliminar dados do dispositivo.

Data Box Edge e Data Box Gateway são produtos de transferência de dados online que funcionam como gateways de armazenamento de rede para gerir dados entre o site e o Azure. O Data Box Edge, um dispositivo de rede no local, transfere dados de e para o Azure e utiliza computação edge ativada por inteligência artificial (AI) para processar dados. O Data Box Gateway é uma aplicação virtual com capacidades de gateway de armazenamento.

Saiba mais:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Proteção Avançada Contra Ameaças

O armazenamento do Azure fornece proteção avançada contra ameaças para uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar sua conta de armazenamento. A proteção avançada contra ameaças monitora os logs de diagnóstico do armazenamento do Azure para solicitações de leitura, gravação ou exclusão suspeitas no armazenamento de BLOBs.

Alertas de proteção avançada contra ameaças podem ser exibidos na [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). A central de segurança do Azure fornece detalhes sobre qualquer atividade suspeita detectada e recomenda ações para investigar e corrigir a ameaça em potencial.

Saiba mais:

* [Visão geral da proteção avançada contra ameaças do armazenamento do Azure](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerenciar chaves de criptografia de disco e segredos em sua assinatura do cofre de chaves. Ele também garante que todos os dados nos discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure. Você deve usar Key Vault para auditar as chaves e o uso da política.

Obter mais informações

* [O que é Azure Key Vault?](/azure/key-vault/key-vault-overview)
