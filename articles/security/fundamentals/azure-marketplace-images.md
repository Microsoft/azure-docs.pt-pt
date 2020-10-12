---
title: Recomendações de segurança para Azure Marketplace Images Microsoft Docs
description: Este artigo fornece recomendações para imagens incluídas no mercado
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536387"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do mercado de Azure

A sua imagem deve cumprir estas recomendações de configuração de segurança. Isto ajuda a manter um alto nível de segurança para imagens de soluções de parceiros no Azure Marketplace.

Execute sempre uma deteção de vulnerabilidade de segurança na sua imagem antes de submeter. Se detetar uma vulnerabilidade de segurança na sua própria imagem publicada, deve informar os seus clientes em tempo útil tanto da vulnerabilidade como da sua correção.

## <a name="open-source-based-images"></a>Imagens baseadas em código aberto

| Categoria | Marcar |
| -------- | ----- |
| Segurança                                                     | Instale todos os patches de segurança mais recentes para a distribuição do Linux.                                                                                                                                                                                                              |
| Segurança                                                     | Siga as diretrizes da indústria para garantir a imagem VM para a distribuição específica do Linux.                                                                                                                                                                                     |
| Segurança                                                     | Limitar a superfície de ataque mantendo a pegada mínima apenas com as funções necessárias do Windows Server, funcionalidades, serviços e portas de rede.                                                                                                                                               |
| Segurança                                                     | Verifique o código-fonte e a imagem VM resultante para malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD apenas inclui contas bloqueadas necessárias que não possuam senhas padrão que permitam o login interativo; sem portas traseiras.                                                                                                                                           |
| Segurança                                                     | Desative as regras de firewall, a menos que a aplicação dependa funcionalmente delas, como um aparelho de firewall.                                                                                                                                                                             |
| Segurança                                                     | Remova todas as informações sensíveis da imagem VHD, tais como chaves SSH de teste, ficheiros de anfitriões conhecidos, ficheiros de registo e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | Evite utilizar o LVM.                                                                                                                                                                                                                                            |
| Segurança                                                     | Inclua as versões mais recentes das bibliotecas necessárias: </br> - OpenSSL v1.0 ou superior </br> - Python 2.5 ou superior (Python 2.6+ é altamente recomendado) </br> - Pacote Python pyasn1 se ainda não estiver instalado </br> - d.OpenSSL v 1.0 ou maior                                                                |
| Segurança                                                     | Entradas claras da história bash/Shell.                                                                                                                                                                                                                                             |
| Redes                                                   | Inclua o servidor SSH por predefinição. Set SSH mantenha-se vivo para sshd config com a seguinte opção: ClientAliveInterval 180.                                                                                                                                                        |
| Redes                                                   | Remova qualquer configuração de rede personalizada da imagem. Eliminar o resolve.conf: `rm /etc/resolv.conf` .                                                                                                                                                                                |
| Implementação                                                   | Instale o mais recente agente Azure Linux.</br> - Instalar utilizando o pacote RPM ou Deb.  </br> - Também pode utilizar o processo de instalação manual, mas as embalagens do instalador são recomendadas e preferidas. </br> - Se instalar o agente manualmente a partir do repositório GitHub, primeiro copie o `waagent` ficheiro para e corra `/usr/sbin` (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O ficheiro de configuração do agente é colocado em `/etc/waagent.conf` . |
| Implementação                                                   | Certifique-se de que o Suporte Azure pode fornecer aos nossos parceiros a saída da consola em série quando necessário e fornecer um tempo suficiente para a montagem do disco de SO a partir do armazenamento em nuvem. Adicione os seguintes parâmetros à imagem Kernel Boot Line: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` . |
| Implementação                                                   | Sem troca de divisórias no disco de so. O Swap pode ser solicitado para criação no disco de recursos local pelo Agente Linux.         |
| Implementação                                                   | Crie uma única divisória de raiz para o disco DE.      |
| Implementação                                                   | Sistema operativo de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas em servidores do Windows

| Categoria | Marcar |
|--------- | ----- |
| Segurança                                                         | Utilize uma imagem base de SO segura. O VHD utilizado para a origem de qualquer imagem baseada no Windows Server deve ser a partir das imagens OOS do Windows Server fornecidas através do Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recentes.                                                                                                                                     |
| Segurança                                                         | As aplicações não devem depender de nomes de utilizadores restritos, como administrador, raiz ou administrador.                                                                |
| Segurança                                                         | Ativar a encriptação bitLocker Drive tanto para discos rígidos oss como para os discos rígidos de dados.                                                             |
| Segurança                                                         | Limitar a superfície de ataque mantendo a pegada mínima apenas com as funções necessárias do Windows Server, funcionalidades, serviços e portas de rede ativadas.                         |
| Segurança                                                         | Verifique o código-fonte e a imagem VM resultante para malware.                                                                                                                     |
| Segurança                                                         | Definir atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD apenas inclui contas bloqueadas necessárias que não possuam senhas padrão que permitam o login interativo; sem portas traseiras.                             |
| Segurança                                                         | Desative as regras de firewall, a menos que a aplicação dependa funcionalmente delas, como um aparelho de firewall.                                                               |
| Segurança                                                         | Remova todas as informações sensíveis da imagem VHD, incluindo ficheiros HOSTS, ficheiros de registo e certificados desnecessários.                                              |
| Implementação                                                       | Sistema operativo de 64 bits.                            |

Mesmo que a sua organização não tenha imagens no mercado Azure, considere verificar as configurações de imagem do Windows e Linux contra estas recomendações.

