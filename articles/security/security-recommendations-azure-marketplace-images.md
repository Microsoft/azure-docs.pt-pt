---
title: Recomendações de segurança para imagens do Azure Marketplace | Documentos da Microsoft
description: Este artigo fornece recomendações para incluído no mercado de imagens
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: fa521b81c95f7c0556b082e5487848ef4d7ecaf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444117"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do Azure Marketplace

Recomendamos que cada solução está em conformidade com as seguintes recomendações de configuração de segurança. Isto ajuda a manter um elevado nível de segurança para imagens de solução de parceiro no Azure Marketplace.

Estas recomendações também podem ser útil para as organizações que não têm imagens no Azure marketplace. Convém verificar as configurações de imagem de Windows e Linux da sua empresa contra as diretrizes foram encontradas nas tabelas seguintes:

## <a name="open-source-based-images"></a>Abra imagens com base na origem

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificação**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Todos os patches de segurança mais recentes para a distribuição de Linux são instalados.                                                                                                                                                                                                              |
| Segurança                                                     | Diretrizes da indústria para proteger a imagem de VM para a distribuição de Linux específica foram seguidas.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque ao manter a superfície mínima com apenas as funções necessárias do Windows Server, funcionalidades, serviços e portas de rede.                                                                                                                                               |
| Segurança                                                     | Digitalize o código-fonte e a imagem de VM resultante para o malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas contas bloqueadas necessárias, que não dispõem de palavras-passe de predefinição que permitiria que o início de sessão interativo; Não existem portas abertas.                                                                                                                                           |
| Segurança                                                     | Regras de firewall estão desativadas, a menos que o aplicativo funcionalmente baseia-se nos mesmos, como uma aplicação de firewall.                                                                                                                                                                             |
| Segurança                                                     | Todas as informações confidenciais foi removidas da imagem de VHD, como chaves SSH do teste, conhecido certificados desnecessários, ficheiros de registo e ficheiro de anfitriões.                                                                                                                                       |
| Segurança                                                     | Recomenda-se que LVM deve não utilizado.                                                                                                                                                                                                                                            |
| Segurança                                                     | Versões mais recentes das bibliotecas necessárias devem ser incluídas: </br> -Versões do OpenSSL 1.0 ou superior </br> -Python 2.5 ou superior (Python 2.6 + altamente recomendado) </br> -Pacote do Python pyasn1 se ainda não estiver instalado </br> -d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | Entradas do histórico bash/Shell tem de ser limpo                                                                                                                                                                                                                                             |
| Redes                                                   | Servidor SSH deve ser incluído por predefinição. Definir SSH keep alive ao ficheiro sshd config com a seguinte opção: ClientAliveInterval 180                                                                                                                                                        |
| Redes                                                   | Imagem não deve conter qualquer configuração de rede personalizada. Elimine o resolv: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Implementação                                                   | Deve ser instalado o agente de Linux mais recente do Azure </br> -O agente deve ser instalado utilizando o pacote Deb ou RPM.  </br> – Também pode utilizar o processo de instalação manual, mas os pacotes de instalador são recomendados e preferencial. </br> – Se instalar o agente manualmente a partir do repositório do GitHub, primeiro copie o `waagent` de ficheiros para `/usr/sbin` e executar (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O ficheiro de configuração do agente é colocado em `/etc/waagent.conf`.    |
| Implementação                                                   | Garante que o suporte do Azure pode proporcionar os nossos parceiros com a consola de série de saída quando for necessário e fornecer um tempo limite adequado para a montagem do disco de SO de armazenamento na cloud. Imagem tem de ter a adicionado os seguintes parâmetros à linha de arranque de Kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implementação                                                   | Nenhuma partição de troca no disco do SO. Troca pode ser pedida para a criação no disco de recurso local pelo agente do Linux.         |
| Implementação                                                   | Recomenda-se que uma partição de raiz única é criada para o disco do SO.      |
| Implementação                                                   | 64 bits apenas sistema de operativo.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens do Windows baseados em servidor

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificação**                                                                                                                                                                |
| Segurança                                                         | Utilize uma imagem de base do sistema operacional segura. O VHD utilizado para a origem de qualquer imagem com base no Windows Server tem de ter entre as imagens de SO do Windows Server fornecidas por meio do Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recente.                                                                                                                                     |
| Segurança                                                         | Aplicativos não devem ter uma dependência nos nomes de utilizadores restritos, como administrador, de raiz e de administrador.                                                                |
| Segurança                                                         | A criptografia de unidade BitLocker não é suportada no disco rígido do sistema operativo. O BitLocker pode ser utilizado em discos de dados.                                                            |
| Segurança                                                         | Limite a superfície de ataque ao manter a superfície mínima com apenas as funções necessárias do Windows Server, funcionalidades, serviços e portas ativadas de rede.                         |
| Segurança                                                         | Digitalize o código-fonte e a imagem de VM resultante para o malware.                                                                                                                     |
| Segurança                                                         | Definir a atualização de segurança de imagens do Windows Server para a atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas contas bloqueadas necessárias, que não dispõem de palavras-passe de predefinição que permitiria que o início de sessão interativo; Não existem portas abertas.                             |
| Segurança                                                         | Regras de firewall estão desativadas, a menos que o aplicativo funcionalmente baseia-se nos mesmos, como uma aplicação de firewall.                                                               |
| Segurança                                                         | Todas as informações confidenciais foi removidas da imagem de VHD. Por exemplo, ficheiro de anfitriões, ficheiros de registo e certificados desnecessários devem ser removidos.                                              |
| Implementação                                                       | 64 bits apenas sistema de operativo.                            |
