---
title: Azure Files e Azure NetApp Comparação de ficheiros | Microsoft Docs
description: Comparação de Ficheiros Azure e Ficheiros Azure NetApp.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 652d362a11e80a488c9278dfeff38e715acee784
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641941"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Comparação de ficheiros Azure e Azure NetApp

Este artigo fornece uma comparação de Ficheiros Azure e Ficheiros Azure NetApp. 

A maioria das cargas de trabalho que requerem armazenamento de ficheiros em nuvem funcionam bem em ficheiros Azure ou Azure NetApp. Para ajudar a determinar o melhor ajuste para a sua carga de trabalho, reveja as informações fornecidas neste artigo. Para obter mais informações, consulte a documentação dos [Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/) e [do Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/)

## <a name="features"></a>Funcionalidades

| Categoria | Ficheiros do Azure | Azure NetApp Files |
|---------|-------------------------|---------|
| Description | [O Azure Files](https://azure.microsoft.com/services/storage/files/) é um serviço totalmente gerido e altamente disponível e otimizado para cargas de trabalho de acesso aleatório com atualizações de dados no local.<br><br> O Azure Files é construído na mesma plataforma de armazenamento Azure que outros serviços como o Azure Blobs. | [O Azure NetApp Files](https://azure.microsoft.com/services/netapp/) é um serviço NAS totalmente gerido, altamente disponível e de qualidade empresarial que consegue lidar com as cargas de trabalho mais exigentes, de alto desempenho e baixa latência que requerem capacidades avançadas de gestão de dados. Permite a migração de cargas de trabalho, que são consideradas "não migradoras" sem.<br><br>  A ANF é construída sobre o metal nu da NetApp com o sistema de armazenamento ONTAP a funcionar dentro do datacenter Azure para uma experiência Azure consistente e um desempenho no local como desempenho. |
| Protocolos | Premium<br><ul><li>SMB 2.1, 3.0</li><li>NFS 4.1 (pré-visualização)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0</li><li>REST</li></ul><br> Para saber mais, consulte [os protocolos de partilha de ficheiros disponíveis.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols) | Todos os níveis<br><ul><li>SMB 1, 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>Acesso a protocolo duplo (NFSv3/SMB)</li></ul><br> Para saber mais, consulte como criar [volumes NFS,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)ou [duplos protocolos.](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) |
| Disponibilidade da Região | Premium<br><ul><li>Mais de 30 Regiões</li></ul><br>Standard<br><ul><li>Todas as regiões</li></ul><br> Para saber mais, veja [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Todos os níveis<br><ul><li>25+ Regiões</li></ul><br> Para saber mais, veja [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundância | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Para saber mais, consulte [a redundância.](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy) | Todos os níveis<br><ul><li>HA local embutido</li><li>[Replicação entre regiões](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Acordo Service-Level (SLA)<br><br> Note que os SLAs para Ficheiros Azure e Ficheiros Azure NetApp são calculados de forma diferente. | [SLA para ficheiros Azure](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA para ficheiros Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Autenticação e Autorização Identity-Based | SMB<br><ul><li>Serviços de Domínio do Active Directory (AD DS)</li><li>Serviços de Domínio do Diretório Ativo Azure (Azure AD DS)</li></ul><br> Note que a autenticação baseada na identificação só é suportada quando se utiliza o protocolo SMB. Para saber mais, consulte [as FAQ.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) | SMB<br><ul><li>Serviços de Domínio do Active Directory (AD DS)</li><li>Serviços de Domínio do Diretório Ativo Azure (Azure AD DS)</li></ul><br> Protocolo duplo NFS/SMB<ul><li>Integração DE ADDS/LDAP</li></ul><br>NFSv3/NFSv4.1<ul><li>Integração DE ADDS/LDAP (vindo)</li><li>Grupos alargados NFS (vindo)</li></ul><br> Para saber mais, consulte [as FAQ.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs) |
| Encriptação | SMB<br><ul><li>Encriptação em repouso (AES 256) com chaves geridas pelo cliente ou pela Microsoft</li><li>Encriptação Kerberos utilizando AES 256 ou RC4-HMAC</li><li>Encriptação de dados em circulação</li></ul><br>NFS<br><ul><li>Encriptação em repouso (AES 256) com chaves geridas pelo cliente ou pela Microsoft</li></ul><br>REST<br><ul><li>Encriptação em repouso (AES 256) com chaves geridas pelo cliente ou pela Microsoft</li><li>Encriptação de dados em circulação</li></ul><br> Para saber mais, consulte [a segurança.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security) | Todos os protocolos<br><ul><li>Encriptação em repouso (AES 256) com teclas geridas pela Microsoft </li></ul><br>NFS 4.1<ul><li>Encriptação em trânsito utilizando Kerberos com AES 256</li></ul><br> Para saber mais, consulte [as FAQ de segurança.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs) |
| Opções de Acesso | <ul><li>Internet</li><li>Acesso seguro vNet</li><li>Gateway de VPN</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Para saber mais, consulte [considerações de rede.](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) | <ul><li>Acesso seguro vNet</li><li>Gateway de VPN</li><li>ExpressRoute</li><li>[Cache global de arquivos](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Para saber mais, consulte [considerações de rede.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) |
| Proteção de Dados  | <ul><li>Instantâneos incrementais</li><li>Auto-restauro do utilizador de ficheiros/diretórios</li><li>Restaurar para nova localização</li><li>Reverter no lugar</li><li>Excluir suave de nível de partilha</li><li>Integração de Backup Azure</li></ul><br> Para saber mais, consulte [o Azure Files melhora as capacidades de proteção de dados.](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/) | <ul><li>Instantâneos (255/volume)</li><li>Auto-restauro do utilizador de ficheiros/diretórios</li><li>Restaurar para novo volume</li><li>Reverter no lugar</li><li>[Replicação cross-region (pré-visualização](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) pública)</li></ul><br> Para saber mais, consulte [como funcionam as fotos dos Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Ferramentas de migração  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Serviço de Migração de Armazenamento</li><li>AzCopy</li><li>Robocopy</li></ul><br> Para saber mais, consulte [as ações de ficheiros Migrate to Azure](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview). | <ul><li>[Cache global de arquivos](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Serviço de Migração de Armazenamento</li><li>AzCopy</li><li>Robocopy</li><li>Baseado em aplicações (por exemplo, HSR, Data Guard, AOAG)</li></ul> |
| Escalões | <ul><li>Premium</li><li>Transação Otimizada</li><li>Frequente</li><li>Esporádico</li></ul><br> Para saber mais, consulte [os níveis de armazenamento.](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers) | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Todos os níveis fornecem latência mínima sub-ms.<br><br> Para saber mais, consulte [os Níveis de Serviço](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) e [considerações de desempenho.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations) |
| Preços | [Preços dos ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/) | [Preços dos ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Escalabilidade e desempenho

| Categoria | Ficheiros do Azure | Azure NetApp Files |
|---------|---------|---------|
| Tamanho mínimo de partilha/volume | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Todos os níveis<br><ul><li>100 GiB (tamanho mínimo da piscina: 4 TiB)</li></ul> |
| Tamanho máximo de partilha/volume | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Todos os níveis<br><ul><li>100 TiB (limite de 500-TiB de capacidade)</li></ul><br>Até 12,5 PiB por conta Azure NetApp. |
| IOPS máximos de quota/volume | Premium<br><ul><li>Até 100k</li></ul><br>Standard<br><ul><li>Até 10k</li></ul> | Ultra e Premium<br><ul><li>Até 450k </li></ul><br>Standard<br><ul><li>Até 320k</li></ul> |
| Produção máxima de ações/volume | Premium<br><ul><li>Até 10 GiB/s</li></ul><br>Standard<br><ul><li>Até 300 MiB/s</li></ul> | Ultra e Premium<br><ul><li>Até 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Até 3.2GiB/s</li></ul> |
| Tamanho Máximo do Ficheiro | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Todos os níveis<br><ul><li>16 TiB</li></ul> |
| IOPS máximos por ficheiro | Premium<br><ul><li>Até 8.000</li></ul><br>Standard<br><ul><li>1,000</li></ul> | Todos os níveis<br><ul><li>Até ao limite de volume</li></ul> |
| Limite máximo per arquivo | Premium<br><ul><li>300 MiB/s (Até 1 GiB/s com SMB multicanal)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Todos os níveis<br><ul><li>Até ao limite de volume</li></ul> |
| SMB Multicanal | Sim[(Pré-visualização)](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance) | Yes |
| Latência | Latência mínima de um milissegundo (2ms a 3ms para pequena IO) | Latência mínima sub-milisegundo (<1ms para IO aleatório)<br><br>Para saber mais, consulte [os parâmetros de desempenho.](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) |

Para obter mais informações sobre a escalabilidade e os objetivos de desempenho, consulte [os Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) e [os Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits).

## <a name="next-steps"></a>Passos Seguintes
* [Documentação dos Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/)
* [Documentação dos Ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/)
