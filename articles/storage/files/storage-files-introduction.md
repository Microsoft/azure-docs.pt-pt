---
title: Introdução aos Ficheiros do Azure | Microsoft Docs
description: Uma descrição geral dos Ficheiros do Azure, um serviço que lhe permite criar e utilizar partilhas de ficheiros de rede na cloud com o protocolo SMB padrão da indústria.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 03/10/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3a7bb7939921bfcc3a870193cb9025a8f3dff72c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78969137"
---
# <a name="what-is-azure-files"></a>O que são os Ficheiros do Azure?
Os Ficheiros do Azure oferecem partilhas de ficheiros completamente geridas na cloud que são acessíveis através do [protocolo SMB (Server Message Block) norma da indústria](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). As partilhas de ficheiros do Azure podem ser montadas em simultâneo por implementações na cloud ou no local do Windows, Linux e macOS. Além disso, as partilhas de ficheiros do Azure podem ser colocadas em cache em Servidores Windows com o Azure File Sync para acesso rápido perto do local onde os dados estão a ser utilizados.

## <a name="videos"></a>Vídeos
| Apresentação da Sincronização de Ficheiros do Azure (2 m) | Ficheiros do Azure com Sincronização (Ignite 2017) (85 m)  |
|-|-|
| [![Screencast do vídeo Introduceing Azure File Sync - clique para reproduzir!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast dos Ficheiros Azure com apresentação sync - clique para reproduzir!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/watch?v=GMzh2M66E9o) |

## <a name="why-azure-files-is-useful"></a>Por que é que os Ficheiros do Azure são úteis
As partilhas de ficheiros do Azure podem ser utilizadas para:

* **Substituir ou complementar os servidores de ficheiros no local**:  
    Os Ficheiros do Azure podem ser utilizados para substituir completamente ou complementar os servidores de ficheiros no local ou em dispositivos NAS tradicionais. Os sistemas operativos populares, como o Windows, macOS e Linux, podem montar diretamente partilhas de ficheiros do Azure a partir de qualquer ponto do mundo. As partilhas de ficheiros do Azure também podem ser replicadas com o Azure File Sync em Servidores Windows, no local ou na cloud, para desempenho e colocação em cache distribuída dos dados no local onde estão a ser utilizados. Com o recente lançamento da [Autenticação AD Da Azure Files,](storage-files-active-directory-overview.md)as ações de ficheiros Azure podem continuar a trabalhar com a AD alojada no local para controlo de acesso. 

* **Aplicações "Levantar e mudar":**  
    Os Ficheiros do Azure facilitam a migração lift-and-shift de aplicações para a cloud, que esperam que uma partilha de ficheiros armazene dados de utilizador e aplicações de ficheiros. Os Ficheiros do Azure permitem tanto o cenário de migração lift-and-shift "clássico", no qual a aplicação e os respetivos dados são movidos para o Azure, como o cenário de migração lift-and-shift "híbrido", no qual os dados da aplicação são movidos para os Ficheiros do Azure e a aplicação continua a ser executada no local. 

* Simplificar o **desenvolvimento das nuvens:**  
    Os Ficheiros do Azure também podem ser utilizados de várias formas para simplificar novos projetos de desenvolvimento na cloud. Por exemplo:
    * **Definições de aplicação partilhada:**  
        Um padrão comum para aplicações distribuídas consiste em ter ficheiros de configuração numa localização centralizada, onde podem ser acedidos a partir de muitas instâncias da aplicação. As instâncias da aplicação podem carregar a respetiva configuração através da API REST de Ficheiros e as pessoas podem aceder às mesmas conforme necessário ao montarem a partilha de SMB localmente.

    * **Partilha de diagnóstico:**  
        Uma partilha de ficheiros do Azure é um local conveniente para aplicações na cloud, para escrever os seus registos, métricas e informações de falha de sistema. Os registos podem ser escritos pelas instâncias da aplicação através da API REST de Ficheiros e os programadores podem aceder aos mesmos ao montarem a partilha de ficheiros no seu computador local. Isto proporciona uma enorme flexibilidade, uma vez que os programadores podem adotar o desenvolvimento na cloud sem terem de abandonar quaisquer ferramentas existentes que conhecem e adoram.

    * **Dev/Teste/Decevejo:**  
        Muitas vezes, quando trabalham em VMs na cloud, os programadores ou administradores precisam de um conjunto de ferramentas ou utilitários. Copiar essas ferramentas e utilitários para cada VM pode ser um exercício demorado. Ao montar uma partilha de ficheiros do Azure localmente nas VMs, um programador e o administrador podem aceder rapidamente às suas ferramentas e utilitários, sem ser necessária qualquer cópia.

## <a name="key-benefits"></a>Principais vantagens
* **Acesso partilhado**. As partilhas de ficheiros do Azure suportam o protocolo SMB norma da indústria, o que significa que pode substituir facilmente as suas partilhas de ficheiros no local por partilhas de ficheiros do Azure sem se preocupar com a compatibilidade das aplicações. A capacidade de poder partilhar um sistema de ficheiros entre vários computadores e aplicações/instâncias é uma enorme vantagem com os Ficheiros do Azure para aplicações que requerem capacidade de partilha. 
* **Totalmente gerido.** As partilhas de ficheiros do Azure podem ser criadas sem que seja necessário gerir hardware ou SO. Isto significa que não tem de lidar com a aplicação de patches ao SO do servidor com atualizações de segurança críticas ou substituir os discos rígidos com falhas.
* **Scripting e ferramentas.** Os cmdlets do PowerShell e a CLI do Azure podem ser utilizados para criar, montar e gerir partilhas de ficheiros do Azure como parte da administração das aplicações do Azure. Pode utilizar o portal do Azure e o Explorador de Armazenamento do Azure para criar e gerir partilhas de ficheiros do Azure. 
* **Resiliência**. Os Ficheiros do Azure foram criados de raiz para estarem sempre disponíveis. Substituir as partilhas de ficheiros no local pelos Ficheiros do Azure significa que já não tem de se preocupar com falhas de energia ou problemas de rede. 
* **Programabilidade familiar.** As aplicações que são executadas no Azure podem aceder a dados na partilha através das [APIs de E/S de sistema de ficheiros](https://msdn.microsoft.com/library/system.io.file.aspx). Os programadores, por conseguinte, podem tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Para além das APIs de E/S de Sistema, pode utilizar as [Bibliotecas de Cliente do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn261237.aspx) ou da [API REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Passos Seguintes
* [Criar partilha de ficheiros Azure](storage-how-to-create-file-share.md)
* [Ligar e montar no Windows](storage-how-to-use-files-windows.md)
* [Ligar e montar em Linux](storage-how-to-use-files-linux.md)
* [Ligar e montar no macOS](storage-how-to-use-files-mac.md)
