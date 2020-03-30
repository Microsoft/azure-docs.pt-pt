---
title: Metas de armazenamento Desacato Azure HPC Cache NFS
description: Dicas para evitar e corrigir erros de configuração e outros problemas que podem causar falha ao criar um alvo de armazenamento NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652090"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Problemas de configuração nas e problemas de alvo de armazenamento NFS

Este artigo fornece soluções para alguns erros de configuração comuns e outros problemas que poderiam impedir o Azure HPC Cache de adicionar um sistema de armazenamento NFS como um alvo de armazenamento.

Este artigo inclui detalhes sobre como verificar as portas e como permitir o acesso de raiz a um sistema NAS. Inclui também informações detalhadas sobre questões menos comuns que podem fazer com que a criação de alvos de armazenamento nFS falhe.

> [!TIP]
> Antes de utilizar este guia, leia [os pré-requisitos para os alvos de armazenamento nFS](hpc-cache-prereqs.md#nfs-storage-requirements).

Se a solução para o seu problema não estiver incluída aqui, por favor [abra um bilhete](hpc-cache-support-ticket.md) de suporte para que o Microsoft Service e o Support possam trabalhar consigo para investigar e resolver o problema.

## <a name="check-port-settings"></a>Verifique as definições da porta

A Cache Azure HPC necessita de acesso a várias portas UDP/TCP no sistema de armazenamento NAS de back-end. Certifique-se de que estas portas estão acessíveis no sistema NAS e também que o tráfego é permitido a estas portas através de quaisquer firewalls entre o sistema de armazenamento e a sub-rede cache. Pode ser necessário trabalhar com administradores de firewall e rede para o seu centro de dados verificar esta configuração.

As portas são diferentes para sistemas de armazenamento de diferentes fornecedores, por isso verifique os requisitos do seu sistema ao configurar um alvo de armazenamento.

Em geral, a cache precisa de acesso a estas portas:

| Protocolo | Porta  | Serviço  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | montado   |
| TCP/UDP  | 4047  | status   |

Para aprender as portas específicas necessárias ``rpcinfo`` para o seu sistema, utilize o seguinte comando. Este comando abaixo lista as portas e forma os resultados relevantes numa tabela. (Utilize o endereço IP do seu sistema no lugar do *termo><storage_IP.)*

Pode emitir este comando de qualquer cliente Linux que tenha a infraestrutura NFS instalada. Se utilizar um cliente dentro da sub-rede do cluster, também pode ajudar a verificar a conectividade entre a subnet e o sistema de armazenamento.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Certifique-se de que todas as ``rpcinfo`` portas devolvidas pela consulta permitem tráfego ilimitado a partir da subnet azure HPC Cache.

Verifique estas definições tanto na própria NAS como em quaisquer firewalls entre o sistema de armazenamento e a sub-rede cache.

## <a name="check-root-access"></a>Verifique o acesso à raiz

A Azure HPC Cache precisa de ter acesso às exportações do seu sistema de armazenamento para criar o alvo de armazenamento. Especificamente, monta as exportações como ID 0 do utilizador.

Diferentes sistemas de armazenamento utilizam diferentes métodos para permitir este acesso:

* Os servidores Linux ``no_root_squash`` geralmente adicionam ``/etc/exports``ao caminho exportado em .
* Os sistemas NetApp e EMC normalmente controlam o acesso com regras de exportação que estão ligadas a endereços ou redes IP específicos.

Se utilizar as regras de exportação, lembre-se de que a cache pode utilizar vários endereços IP diferentes da sub-rede cache. Permitir o acesso a partir de toda a gama de possíveis endereços IP da sub-rede.

Trabalhe com o seu fornecedor de armazenamento NAS para permitir o nível certo de acesso para a cache.

### <a name="allow-root-access-on-directory-paths"></a>Permitir o acesso de raiz em caminhos de diretório
<!-- linked in prereqs article -->

Para sistemas NAS que exportam diretórios hierárquicos, o Azure HPC Cache necessita de acesso enraado a cada nível de exportação.

Por exemplo, um sistema pode mostrar três exportações como estas:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

A ``/ifs/accounting/payroll`` exportação é ``/ifs/accounting``uma ``/ifs/accounting`` criança de , ``/ifs``e é em si uma criança de .

Se adicionar ``payroll`` a exportação como um alvo de armazenamento ``/ifs/`` de cache HPC, a cache realmente monta e acede ao diretório de folha de pagamento a partir daí. Assim, o Azure HPC ``/ifs`` Cache necessita de ``/ifs/accounting/payroll`` acesso de raiz para aceder à exportação.

Este requisito está relacionado com a forma como os ficheiros de cache indexam e evitam colisões de ficheiros, utilizando cabos de ficheiros que o sistema de armazenamento fornece.

Um sistema NAS com exportações hierárquicas pode dar diferentes cabos de ficheiro para o mesmo ficheiro se o ficheiro for recuperado de diferentes exportações. Por exemplo, um ``/ifs/accounting`` cliente poderia ``payroll/2011.txt``montar e aceder ao ficheiro . Outro cliente ``/ifs/accounting/payroll`` monta e ``2011.txt``acede ao ficheiro. Dependendo da forma como o sistema de armazenamento atribui cabos de ficheiros, estes ``<mount2>/payroll/2011.txt`` dois clientes ``<mount3>/2011.txt``podem receber o mesmo ficheiro com diferentes cabos de ficheiro (um para e um para ).

O sistema de armazenamento back-end mantém pseudónimos internos para cabos de ficheiros, mas o Azure HPC Cache não consegue dizer quais as pegas de ficheiro na sua referência de índice o mesmo item. Assim, é possível que a cache possa ter escritos diferentes em cache para o mesmo ficheiro, e aplicar as alterações incorretamente porque não sabe que são o mesmo ficheiro.

Para evitar esta possível colisão de ficheiros para ficheiros em múltiplas exportações,``/ifs`` a Azure HPC Cache monta automaticamente a exportação mais lenta disponível no caminho (no exemplo) e utiliza o cabo de ficheiro dado a partir dessa exportação. Se várias exportações utilizarem o mesmo caminho base, o Azure HPC Cache necessita de acesso raiz a esse caminho.

## <a name="enable-export-listing"></a>Permitir a listagem de exportação
<!-- link in prereqs article -->

O NAS deve enumerar as suas exportações quando o Cache Azure HPC a questionar.

Na maioria dos sistemas de armazenamento nFS, pode testar isto enviando a seguinte consulta de um cliente Linux:``showmount -e <storage IP address>``

Utilize um cliente Linux da mesma rede virtual que a sua cache, se possível.

Se esse comando não listar as exportações, a cache terá problemas em ligar-se ao seu sistema de armazenamento. Trabalhe com o seu fornecedor NAS para permitir a listagem de exportação.

## <a name="adjust-vpn-packet-size-restrictions"></a>Ajuste as restrições de tamanho do pacote VPN
<!-- link in prereqs article -->

Se tiver uma VPN entre a cache e o seu dispositivo NAS, a VPN pode bloquear pacotes Ethernet de tamanho completo de 1500 bytes. Poderá ter este problema se as grandes trocas entre o NAS e a instância de Cache Azure HPC não forem concluídas, mas as atualizações mais pequenas funcionam como esperado.

Não há uma maneira simples de dizer se o seu sistema tem ou não este problema, a menos que conheça os detalhes da sua configuração VPN. Aqui estão alguns métodos que podem ajudá-lo a verificar este problema.

* Utilize farejadores de pacotes em ambos os lados da VPN para detetar quais os pacotes que transferem com sucesso.
* Se o seu VPN permitir comandos de ping, pode testar o envio de um pacote de tamanho completo.

  Execute um comando de ping sobre a VPN para o NAS com estas opções. (Utilize o endereço IP do seu sistema de armazenamento no lugar do *valor<storage_IP>.)*

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Estas são as opções no comando:

  * ``-M do``- Não fragmente
  * ``-c 1``- Enviar apenas um pacote
  * ``-s 1472``- Detete o tamanho da carga útil para 1472 bytes. Esta é a carga útil de tamanho máximo para um pacote de 1500 bytes após a contabilização da sobrecarga ethernet.

  Uma resposta com êxito tem o seguinte aspeto:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se o ping falhar com 1472 bytes, poderá ser necessário configurar a fixação de MSS na VPN para que o sistema remoto detete corretamente o tamanho máximo da armação. Leia a documentação dos [parâmetros VPN Gateway IPsec/IKE](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) para saber mais.

## <a name="check-for-acl-security-style"></a>Verifique o estilo de segurança ACL

Alguns sistemas NAS usam um estilo de segurança híbrido que combina listas de controlo de acesso (ACLs) com a segurança posix tradicional ou UNIX.

Se o seu sistema reportar o seu estilo de segurança como UNIX ou POSIX sem incluir o acrónimo "ACL", este problema não o afeta.

Para sistemas que utilizam ACLs, o Azure HPC Cache precisa de rastrear valores adicionais específicos do utilizador para controlar o acesso aos ficheiros. Isto é feito permitindo uma cache de acesso. Não existe um controlo virado para o utilizador para ligar a cache de acesso, mas pode abrir um bilhete de suporte para solicitar que seja ativado para os alvos de armazenamento afetados no seu sistema de cache.

## <a name="next-steps"></a>Passos seguintes

Se tiver um problema que não foi abordado neste artigo, [abra um bilhete](hpc-cache-support-ticket.md) de apoio para obter ajuda especializada.
