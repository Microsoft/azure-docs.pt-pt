---
title: Introdução aos Ficheiros do Azure | Microsoft Docs
description: Uma descrição geral dos Ficheiros do Azure, um serviço que lhe permite criar e utilizar partilhas de ficheiros de rede na cloud com o protocolo SMB padrão da indústria.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070138"
---
# <a name="what-is-azure-files"></a>O que são os Ficheiros do Azure?
O Azure Files oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo padrão do [Bloco de Mensagens do Servidor (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) da indústria ou [do protocolo do Sistema de Ficheiros de Rede (NFS).](https://en.wikipedia.org/wiki/Network_File_System) As ações de ficheiros Azure podem ser montadas simultaneamente por implementações em nuvem ou no local. As ações de ficheiros SMB da Azure Files estão acessíveis a partir de clientes Windows, Linux e macOS. As ações de ficheiros Azure Files NFS estão acessíveis a clientes Linux ou macOS. Além disso, as ações de ficheiroS SMB do Azure Files podem ser colocadas em cache nos Servidores do Windows com o Azure File Sync para um acesso rápido perto do local onde os dados estão a ser utilizados.

## <a name="videos"></a>Vídeos
| Apresentando a Azure File Sync | Ficheiros Azure com Sincronização (Ignite 2019)  |
|-|-|
| [![Screencast do vídeo Azure File Sync - clique para reproduzir!](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast dos Ficheiros Azure com apresentação de Sync - clique para reproduzir!](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Aqui estão alguns vídeos sobre os casos de uso comum de Ficheiros Azure:
* [Substitua o seu servidor de ficheiros por uma partilha de ficheiros Azure sem servidor](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Começando com recipientes de perfil FSLogix em ficheiros Azure em Windows Virtual Desktop alavancando a autenticação de AD](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Por que é que os Ficheiros do Azure são úteis
As partilhas de ficheiros do Azure podem ser utilizadas para:

* **Substituir ou complementar os servidores de ficheiros no local**:  
    Os Ficheiros do Azure podem ser utilizados para substituir completamente ou complementar os servidores de ficheiros no local ou em dispositivos NAS tradicionais. Os sistemas operativos populares, como o Windows, macOS e Linux, podem montar diretamente partilhas de ficheiros do Azure a partir de qualquer ponto do mundo. As ações de ficheiros Azure File SMB também podem ser replicadas com O Azure File Sync para Servidores windows, tanto no local como na nuvem, para desempenho e caching distribuído dos dados onde está a ser utilizado. Com o recente lançamento da [Azure Files AD Authentication](storage-files-active-directory-overview.md), as ações de ficheiros Azure File SMB podem continuar a funcionar com AD hospedados no local para controlo de acesso. 

* **Aplicações de "elevador e mudança":**  
    Os Ficheiros do Azure facilitam a migração lift-and-shift de aplicações para a cloud, que esperam que uma partilha de ficheiros armazene dados de utilizador e aplicações de ficheiros. Os Ficheiros do Azure permitem tanto o cenário de migração lift-and-shift "clássico", no qual a aplicação e os respetivos dados são movidos para o Azure, como o cenário de migração lift-and-shift "híbrido", no qual os dados da aplicação são movidos para os Ficheiros do Azure e a aplicação continua a ser executada no local. 

* **Simplificar o desenvolvimento da nuvem:**  
    Os Ficheiros do Azure também podem ser utilizados de várias formas para simplificar novos projetos de desenvolvimento na cloud. Por exemplo:
    * **Definições de aplicações partilhadas:**  
        Um padrão comum para aplicações distribuídas consiste em ter ficheiros de configuração numa localização centralizada, onde podem ser acedidos a partir de muitas instâncias da aplicação. As instâncias da aplicação podem carregar a respetiva configuração através da API REST de Ficheiros e as pessoas podem aceder às mesmas conforme necessário ao montarem a partilha de SMB localmente.

    * **Parte de diagnóstico:**  
        Uma partilha de ficheiros do Azure é um local conveniente para aplicações na cloud, para escrever os seus registos, métricas e informações de falha de sistema. Os registos podem ser escritos pelas instâncias da aplicação através da API REST de Ficheiros e os programadores podem aceder aos mesmos ao montarem a partilha de ficheiros no seu computador local. Isto proporciona uma enorme flexibilidade, uma vez que os programadores podem adotar o desenvolvimento na cloud sem terem de abandonar quaisquer ferramentas existentes que conhecem e adoram.

    * **Dev/Test/Debug:**  
        Muitas vezes, quando trabalham em VMs na cloud, os programadores ou administradores precisam de um conjunto de ferramentas ou utilitários. Copiar essas ferramentas e utilitários para cada VM pode ser um exercício demorado. Ao montar uma partilha de ficheiros do Azure localmente nas VMs, um programador e o administrador podem aceder rapidamente às suas ferramentas e utilitários, sem ser necessária qualquer cópia.
* **Contentorização:**  
    As ações de ficheiros Azure podem ser utilizadas como volumes persistentes para recipientes imponentes. Os contentores fornecem capacidades de "construir uma vez, correr em qualquer lugar" que permitem aos desenvolvedores acelerar a inovação. Para os recipientes que acedem a dados brutos em cada partida, é necessário um sistema de ficheiros partilhado para permitir que estes contentores acedam ao sistema de ficheiros independentemente do caso em que funcionam.

## <a name="key-benefits"></a>Principais vantagens
* **Acesso partilhado**. As ações de ficheiros Azure suportam os protocolos SMB e NFS padrão da indústria, o que significa que pode substituir perfeitamente as suas ações de ficheiros no local por ações de ficheiros Azure sem se preocupar com a compatibilidade da aplicação. A capacidade de poder partilhar um sistema de ficheiros entre vários computadores e aplicações/instâncias é uma enorme vantagem com os Ficheiros do Azure para aplicações que requerem capacidade de partilha. 
* **Totalmente gerido.** As partilhas de ficheiros do Azure podem ser criadas sem que seja necessário gerir hardware ou SO. Isto significa que não tem de lidar com a aplicação de patches ao SO do servidor com atualizações de segurança críticas ou substituir os discos rígidos com falhas.
* **Scripting e ferramentas**. Os cmdlets PowerShell e Azure CLI podem ser usados para criar, montar e gerir ações de ficheiros Azure como parte da administração de aplicações Azure. Pode criar e gerir ações de ficheiros Azure utilizando o portal Azure e o Azure Storage Explorer. 
* **Resiliência.** Os Ficheiros do Azure foram criados de raiz para estarem sempre disponíveis. Substituir as partilhas de ficheiros no local pelos Ficheiros do Azure significa que já não tem de se preocupar com falhas de energia ou problemas de rede. 
* **Programabilidade familiar.** As aplicações que são executadas no Azure podem aceder a dados na partilha através das [APIs de E/S de sistema de ficheiros](/dotnet/api/system.io.file). Os programadores, por conseguinte, podem tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Para além das APIs de E/S de Sistema, pode utilizar as [Bibliotecas de Cliente do Armazenamento do Azure](/previous-versions/azure/dn261237(v=azure.100)) ou da [API REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

## <a name="next-steps"></a>Passos Seguintes
* [Conheça os protocolos de partilha de ficheiros disponíveis](storage-files-compare-protocols.md)
* [Criar a partilha de ficheiros Azure](storage-how-to-create-file-share.md)
* [Conecte e monte uma partilha SMB no Windows](storage-how-to-use-files-windows.md)
* [Conecte e monte uma partilha SMB no Linux](storage-how-to-use-files-linux.md)
* [Conecte e monte uma partilha SMB no macOS](storage-how-to-use-files-mac.md)
* [Como criar uma quota NFS](storage-files-how-to-create-nfs-shares.md)
