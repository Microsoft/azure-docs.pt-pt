---
title: Lista de verificação de pré-despreocupação para implantar dispositivo GPU Azure Stack Edge Pro / Microsoft Docs
description: Este artigo descreve as informações que podem ser recolhidas antes de implementar o seu dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 82751ea821bb1edfea5dfb353cbb3cdc51fe59d3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903005"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-gpu-device"></a>Lista de verificação de implementação para o seu dispositivo GPU Azure Stack Edge Pro  

Este artigo descreve as informações que podem ser recolhidas antes da implementação real do seu dispositivo Azure Stack Edge Pro. 

Utilize a seguinte lista de verificação para garantir que tem estas informações depois de ter feito uma encomenda para um dispositivo Azure Stack Edge Pro e antes de receber o dispositivo. 

## <a name="deployment-checklist"></a>Lista de verificação de implementação 

| Fase                             | Parâmetro                                                                                                                                                                                                                           | Detalhes                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestão de dispositivos               | <li>Subscrição do Microsoft Azure</li><li>Graph API do Azure Active Directory</li><li>Conta de Armazenamento Microsoft Azure</li>|<li>Habilitado para Azure Stack Edge Pro/Data Box Gateway, permissões de contribuinte</li><li>Garantir o acesso a administradores ou ao utilizador</li><li>Precisa de credenciais de acesso</li> |
| Instalação do dispositivo               | Cabos de alimentação na embalagem. <br>Para os EUA, é enviado um cabo SVE 18/3 com indicação de 125 V e 15 Amperes com um conector NEMA 5-15P para C13 (entrada para saída).                                                                                                                                                                                                          | Fornecido com o dispositivo.<br>Para mais informações, consulte a lista de [cabos de alimentação suportados por país](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>Pelo menos 1 x 1-GbE RJ-45 cabo de rede para a porta 1  </li><li> Pelo menos 1 x 25-GbE Cabo de cobre SFP+ para a Porta 3, Porta 4, Porta 5 ou Porto 6</li>| O cliente precisa de adquirir estes cabos.<br>Para obter uma lista completa de cabos de rede suportados, interruptores e transceptores para cartões de rede de dispositivos, consulte a [Matriz de Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e [os produtos compatíveis com o adaptador de rede de canais 25G ConnectX-4 da porta dupla Mellanox 25G ConnectX-4](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Primeira ligação do dispositivo      | A Porta 1 tem um IP fixo (192.168.100.10/24) para ligação inicial. <li>Requer um portátil para ligação direta à Porta 1, com um endereço IP na rede 192.168.100.0/24.</li><li> Utilize uI local do dispositivo em: `https://192.168.100.10` para uma configuração posterior.</li><li> Um interruptor mínimo de 1 GbE deve ser utilizado para o dispositivo uma vez que a configuração inicial esteja completa. A UI web local não estará acessível se o interruptor ligado não for pelo menos 1 Gbe.</li>|                                                                                                                   |
| Início de são do dispositivo                      | A palavra-passe do administrador do dispositivo deve ter entre 8 e 16 caracteres. <br>Deve conter três dos seguintes caracteres: maiúsculas, minúsculas, numéricas e caracteres especiais.                                            | A palavra-passe predefinida é *a Palavra-passe1* que expira à primeira entrada.                                                     |
| Definições de rede                  | O dispositivo vem com portas de rede de 2 x 1 GbE, 4 x 25 GbE. <li>A porta 1 é utilizada apenas para configurar as definições de gestão. Uma ou mais portas de dados podem ser conectadas e configuradas. </li><li> Pelo menos uma interface de rede de dados entre o Porto 2 - Porto 6 precisa de ser ligada à Internet (com conectividade com a Azure).</li><li> As definições IP suportam a configuração DHCP/IPv4 estática. | A configuração estática do IPv4 requer IP, servidor DNS e gateway predefinido.                                                                                                                  |
| Definições de rede de cálculo     | <li>Requer 2 IPs públicos estáticos para nós Kubernetes e pelo menos 1 IP estático para o serviço Azure Stack Edge Pro Hub aceder a módulos de computação.</li><li>Requer um IP para cada serviço ou recipiente extra que precise de ser acedido externamente a partir do exterior do cluster Kubernetes.</li>                                                                                                                       | Apenas a configuração estática do IPv4 é suportada.                                                                      |
| (Opcional) Definições de procuração web     | <li>Servidor de procuração web IP/FQDN, porta </li><li>Nome de utilizador de procuração web, senha</li>                                                                                                                                                                                                    | Atualmente não suportado com configuração de computação.                                                                     |
| Firewall e definições de porta        | Utilize os [padrões e portas dos URLs listados](azure-stack-edge-system-requirements.md#networking-port-requirements) para permitir os IPs do dispositivo.                                                                                                                                                  |                                                                                                                   |
| (Opcional) Endereço MAC            | Se o endereço MAC precisar de ser whitelist, obtenha o endereço da porta conectada da UI local do dispositivo. |                                                                                                                   |
| (Opcional) Porta de comutação de rede    | O dispositivo acolhe VMs Hiper-V para cálculo. Algumas configurações da porta do comutador de rede não acomodam estas configurações por defeito.                                                                                                        |                                                                                                                   |
| (Recomendado) Definições de tempo       | Configure o fuso horário, o servidor NTP primário, o servidor NTP secundário.                                                                                                                                                                    | Configure o servidor NTP primário e secundário na rede local.<br>Se o servidor local não estiver disponível, os servidores NTP públicos podem ser configurados.                                                    |
| (Opcional) Atualizar as definições do servidor | <li>Requerer o endereço IP do servidor de atualização na rede local, caminho para o servidor WSUS. </li> | Por predefinição, o servidor de atualização do Windows público é utilizado.|
| Definições do dispositivo                   | <li>Nome do dispositivo registado na organização DNS </li><li>Domínio DNS</li> |                                                                                                                   |
| (Opcional) Certificados                      | Utilize os certificados gerados pelo dispositivo <br><br> Se trouxer os seus próprios certificados, precisa: <li>Certificado de assinatura de raiz fidedigno em formato DER com *.cer* </li><li>Certificados de ponto final em formato *pfx*</li>|Os certificados endpoint incluem para Azure Resource Manager, Blob storage, Local web UI.                                                                                                                   |
| Ativação                        | Requerer a chave de ativação a partir do recurso Azure Stack Edge Pro/ Data Box Gateway.                                                                                                                                                       | Uma vez gerada, a chave expira em 3 dias.                                                                        |


## <a name="next-steps"></a>Passos seguintes

Prepare-se para implementar o seu [dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).



