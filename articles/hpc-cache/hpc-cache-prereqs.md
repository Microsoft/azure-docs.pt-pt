---
title: Pré-requisitos de cache do Azure HPC
description: Pré-requisitos para usar o cache HPC do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 50c60e38b58815be04cfb892c3622b9579529e67
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036857"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Pré-requisitos para o cache HPC do Azure

Antes de usar o portal do Azure para criar um novo cache do Azure HPC, verifique se o seu ambiente atende a esses requisitos.

## <a name="azure-subscription"></a>Subscrição do Azure

Uma assinatura paga é recomendada.

> [!NOTE]
> Durante a versão de visualização, a equipe de cache do Azure HPC deve adicionar sua assinatura à lista de acesso antes de poder ser usada para criar uma instância de cache. Esse procedimento ajuda a garantir que cada cliente obtenha uma capacidade de resposta de alta qualidade de seus caches de teste. Preencha [este formulário](https://aka.ms/onboard-hpc-cache) para solicitar acesso.

## <a name="network-infrastructure"></a>Infraestrutura de rede

Duas opções relacionadas à rede devem ser configuradas antes que você possa usar o cache:

* Uma sub-rede dedicada para a instância de cache do HPC do Azure
* Suporte a DNS para que o cache possa acessar o armazenamento e outros recursos

### <a name="cache-subnet"></a>Sub-rede de cache

O cache HPC do Azure precisa de uma sub-rede dedicada com estas qualidades:

* A sub-rede deve ter pelo menos 64 endereços IP disponíveis.
* A sub-rede não pode hospedar nenhuma outra VM, mesmo para serviços relacionados, como computadores cliente.
* Se você usar várias instâncias de cache, cada uma precisará de sua própria sub-rede.

A prática recomendada é criar uma nova sub-rede para o cache. Você pode criar uma nova rede virtual e uma sub-rede como parte da criação do cache.

### <a name="dns-access"></a>Acesso DNS

O cache HPC do Azure precisa do DNS para acessar recursos fora de sua rede virtual. Dependendo de quais recursos você está usando, talvez seja necessário configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure: 

* Para acessar os pontos de extremidade do armazenamento de BLOBs do Azure e outros recursos internos, você precisa do servidor DNS baseado no Azure.
* Para acessar o armazenamento local, você precisa configurar um servidor DNS personalizado que possa resolver seus nomes de host de armazenamento.

Se você só precisar de acesso ao armazenamento de BLOBs, poderá usar o servidor DNS padrão fornecido pelo Azure para seu cache. No entanto, se você precisar de acesso a outros recursos, deverá criar um servidor DNS personalizado e configurá-lo para encaminhar as solicitações de resolução específicas do Azure para o servidor DNS do Azure. (Um servidor DNS simples também pode ser usado para balancear a carga de conexões de cliente entre todos os pontos de montagem de cache disponíveis.)

Saiba mais sobre as configurações de servidor DNS e redes virtuais do Azure na [resolução de nomes para recursos em redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Permissões

Verifique esses pré-requisitos relacionados à permissão antes de começar a criar seu cache.

* O cache HPC do Azure precisa ser capaz de criar NICs (interfaces de rede virtual). O usuário que cria o cache deve ter privilégios suficientes na assinatura para criar NICs.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Se estiver usando o armazenamento de BLOBs, a instância de cache do Azure HPC precisará de autorização para acessar sua conta de armazenamento. Você pode usar o RBAC (controle de acesso baseado em função) para conceder ao cache acesso ao armazenamento de BLOBs. São necessárias duas funções: Colaborador de conta de armazenamento e colaborador de dados de blob de armazenamento. Siga as instruções em [Adicionar armazenamento ao cache](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account).

## <a name="storage-infrastructure"></a>Infraestrutura de armazenamento

O cache dá suporte a exportações de armazenamento NFS ou de contêineres de blob do Azure. Você pode definir destinos de armazenamento ao criar o cache, mas também pode adicioná-los posteriormente. 

### <a name="nfs-storage-requirements"></a>Requisitos de armazenamento NFS

Se você estiver usando o armazenamento de hardware local, o cache precisará ter acesso à rede de largura de banda alta para o datacenter a partir de sua sub-rede. O [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou acesso semelhante é recomendado.

O armazenamento de back-end do NFS deve ser uma plataforma de hardware/software compatível. Contate a equipe de cache do Azure HPC para obter detalhes.

### <a name="blob-storage-requirements"></a>Requisitos de armazenamento de BLOBs

Se você quiser usar o armazenamento de BLOBs do Azure com o cache do HPC do Azure, precisará de uma conta de armazenamento compatível e de um contêiner de blob vazio ou de um contêiner que é preenchido com os dados formatados do cache HPC do Azure, conforme descrito em [mover dados para o armazenamento de BLOBs do Azure](hpc-cache-ingest.md).

Crie a conta e o contêiner antes de tentar adicioná-lo como um destino de armazenamento.

Para criar uma conta de armazenamento compatível, use estas configurações:

* Desempenho **Standard**
* Tipo de conta: **StorageV2 (uso geral v2)**
* Replicação **Armazenamento localmente redundante (LRS)**
* Camada de acesso (padrão): **acesso frequente**

É uma boa prática usar uma conta de armazenamento no mesmo local que o cache.

Você também deve conceder ao aplicativo de cache acesso à sua conta de armazenamento do Azure. Siga a descrição em [Adicionar armazenamento ao cache](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para fornecer ao cache o colaborador de conta de armazenamento de funções de acesso e colaborador de dados do armazenamento de BLOBs. Se você não for o proprietário da conta de armazenamento, faça com que o proprietário execute esta etapa.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma instância de cache do HPC do Azure](hpc-cache-create.md) do portal do Azure
