---
title: Segurança de dados na Austrália do Azure
description: Configuração do Azure nas regiões australianas para atender aos requisitos específicos da política, regulamentos e leis do governo australiano.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608344"
---
# <a name="data-security-in-azure-australia"></a>Segurança de dados na Austrália do Azure

Os princípios abrangentes para proteger os dados do cliente são:

* Protegendo dados usando criptografia
* Gerir segredos
* Restringindo o acesso a dados

## <a name="encrypting-your-data"></a>Criptografando seus dados

A criptografia de dados pode ser aplicada no nível do disco (em repouso), em bancos de dado (em repouso e em trânsito), em aplicativos (em trânsito) e enquanto estiver na rede (em trânsito). Há várias maneiras de obter a criptografia no Azure:

|Serviço/recurso|Descrição|
|---|---|
|Encriptação do Serviço de Armazenamento|O Criptografia do Serviço de Armazenamento do Azure está habilitado no nível da conta de armazenamento, resultando em blobs de blocos e blobs de páginas criptografados automaticamente quando gravados no armazenamento do Azure. Quando você ler os dados do armazenamento do Azure, eles serão descriptografados pelo serviço de armazenamento antes de serem retornados. Use o SSE para proteger seus dados sem precisar modificar ou adicionar código a nenhum aplicativo.|
|Azure Disk Encryption|Use Azure Disk Encryption para criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual do Azure. A integração com o Azure Key Vault lhe dá controle e ajuda você a gerenciar chaves de criptografia de disco.|
|Criptografia no nível do aplicativo do lado do cliente|A criptografia do lado do cliente é incorporada às bibliotecas de cliente de armazenamento do Java e do .NET, que podem utilizar Azure Key Vault APIs, tornando-as simples de implementar. Use Azure Key Vault para obter acesso aos segredos em Azure Key Vault para indivíduos específicos usando Azure Active Directory.|
|Criptografia em trânsito|A criptografia básica disponível para conectividade com o Azure Austrália dá suporte ao protocolo TLS 1,2 e a certificados X. 509. Os algoritmos de criptografia padrão FIPS (FIPS) 140-2 nível 1 também são usados para conexões de rede de infraestrutura entre os data centers da Austrália do Azure.  O Windows Server 2016, o Windows 10, o Windows Server 2012 R2, o Windows 8.1 e os compartilhamentos de arquivos do Azure podem usar SMB 3,0 para criptografia entre a VM e o compartilhamento de arquivos. Use a criptografia do lado do cliente para criptografar os dados antes que eles sejam transferidos para o armazenamento em um aplicativo cliente e para descriptografar os dados depois que eles forem transferidos para fora do armazenamento.|
|VMs IaaS|Use Azure Disk Encryption. Ative Criptografia do Serviço de Armazenamento para criptografar os arquivos VHD que são usados para fazer backup desses discos no armazenamento do Azure, mas isso criptografa apenas os dados gravados recentemente. Isso significa que, se você criar uma VM e, em seguida, habilitar Criptografia do Serviço de Armazenamento na conta de armazenamento que contém o arquivo VHD, somente as alterações serão criptografadas, não o arquivo VHD original.|
|Encriptação do Lado do Cliente|Esse é o método mais seguro para criptografar seus dados, porque criptografa-os antes do trânsito e criptografa os dados em repouso. No entanto, ele requer que você adicione código aos seus aplicativos usando o armazenamento, que talvez você não queira fazer. Nesses casos, você pode usar HTTPS para seus dados em trânsito e Criptografia do Serviço de Armazenamento criptografar os dados em repouso. A criptografia do lado do cliente também envolve mais carga no cliente — você precisa considerar isso em seus planos de escalabilidade, especialmente se estiver Criptografando e Transferindo grandes quantidades de dados.|
|

Para obter mais informações sobre as opções de criptografia no Azure, consulte o [Guia de segurança de armazenamento](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Protegendo dados Gerenciando segredos

O gerenciamento de chaves seguro é essencial para proteger os dados na nuvem. Os clientes devem se esforçar para simplificar o gerenciamento de chaves e manter o controle das chaves usadas por aplicativos e serviços em nuvem para criptografar dados.

### <a name="managing-secrets"></a>Gerir segredos

* Use Key Vault para minimizar os riscos de que os segredos sejam expostos por meio de arquivos de configuração embutidos em código, scripts ou código-fonte. Azure Key Vault criptografa chaves (como as chaves de criptografia para Azure Disk Encryption) e segredos (como senhas), armazenando-as em módulos de segurança de hardware (HSMs) validados pelo FIPS 140-2 nível 2. Para garantia adicional, você pode importar ou gerar chaves nesses HSMs.
* Os modelos e o código do aplicativo devem conter apenas referências de URI aos segredos (o que significa que os segredos reais não estão no código, na configuração ou nos repositórios de código-fonte). Isso impede os principais ataques de phishing em repositórios internos ou externos, como o colheita-bots no GitHub.
* Utilize controles de RBAC fortes dentro de Key Vault. Se um operador confiável deixar a empresa ou for transferido para um novo grupo dentro da empresa, ele deverá ser impedido de poder acessar os segredos.  

Para obter mais informações, consulte [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Isolamento para restringir o acesso a dados

O isolamento é sobre o uso de limites, segmentação e contêineres para limitar o acesso a dados somente a usuários, serviços e aplicativos autorizados. Por exemplo, a separação entre locatários é um mecanismo de segurança essencial para plataformas de nuvem de vários locatários, como Microsoft Azure. O isolamento lógico ajuda a impedir que um locatário interfira com as operações de qualquer outro locatário.

#### <a name="per-customer-isolation"></a>Isolamento por cliente

O Azure implementa o controle de acesso à rede e a separação por meio do isolamento de VLAN de camada 2, listas de controle de acesso, balanceadores de carga e filtros de IP.

Os clientes podem isolar ainda mais seus recursos entre assinaturas, grupos de recursos, redes virtuais e sub-redes.

Para obter mais informações sobre isolamento no Microsoft Azure, consulte o [isolamento na nuvem pública do Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Passos seguintes

Examine o artigo sobre o [Gateway de VPN do Azure](vpn-gateway.md)