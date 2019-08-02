---
title: Ferramentas para trabalhar com o armazenamento do Azure | Microsoft Docs
description: Uma lista de ferramentas que permitem exibir/interagir com os dados do armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 14cbc076f4c9eebd2647cd667acc856b393e4d93
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665741"
---
# <a name="azure-storage-client-tools"></a>Ferramentas de Cliente do Armazenamento do Azure
Os usuários do armazenamento do Azure geralmente desejam poder exibir/interagir com seus dados usando uma ferramenta de cliente de armazenamento do Azure. Nas tabelas a seguir, listamos várias ferramentas que permitem que você faça isso. Colocamos um "X" em cada bloco se ele fornece a capacidade de enumerar e/ou acessar a abstração de dados. A tabela também mostra se as ferramentas estão livres ou não. "Avaliação" indica que há uma avaliação gratuita, mas o produto completo não é gratuito. "Y/N" indica que uma versão está disponível gratuitamente, enquanto uma versão diferente está disponível para compra.

Fornecemos apenas um instantâneo das ferramentas de cliente de armazenamento do Azure disponíveis. Essas ferramentas podem continuar a evoluir e aumentar a funcionalidade. Se houver correções ou atualizações, deixe um comentário para nos informar. O mesmo acontece se você conhecer as ferramentas que não deveria estar aqui-teremos o prazer de adicioná-las.

**Ferramentas de cliente Armazenamento do Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Ferramenta de cliente do armazenamento do Azure</th>
    <th rowspan="2">Blob de Blocos</th>
    <th rowspan="2">Blob de Página</th>
    <th rowspan="2">Acrescentar Blob</th>
    <th rowspan="2">Tabelas</th>
    <th rowspan="2">Filas</th>
    <th rowspan="2">Ficheiros</th>
    <th rowspan="2">Livre</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Portal de Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Explorador de Armazenamento do Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Gerenciador de Servidores</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Ferramentas de cliente de armazenamento do Azure de terceiros**

Não verificamos a funcionalidade ou a qualidade reivindicada pelas seguintes ferramentas de terceiros e sua listagem não implica um endosso da Microsoft.

<table>
  <tr>
    <th rowspan="2">Ferramenta de cliente do armazenamento do Azure</th>
    <th rowspan="2">Blob de Blocos</th>
    <th rowspan="2">Blob de Página</th>
    <th rowspan="2">Acrescentar Blob</th>
    <th rowspan="2">Tabelas</th>
    <th rowspan="2">Filas</th>
    <th rowspan="2">Ficheiros</th>
    <th rowspan="2">Livre</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate Gerenciador do Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Gerenciador de Armazenamento Web do Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>S/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Combinação de nuvem</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Nuvem Gladinet</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
