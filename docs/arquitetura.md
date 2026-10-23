# Arquitetura da Clínica Vida+

## O caminho de uma requisição

```mermaid
sequenceDiagram
    participant N as Navegador do paciente
    participant D as Servidor DNS
    participant S as Servidor da Clínica Vida+

    N->>D: Consulta o endereço da Clínica Vida+
    D-->>N: Retorna o endereço IP do servidor
    N->>S: Estabelece conexão TCP e TLS pela porta 443
    N->>S: GET /consultas/agendar
    S-->>N: 200 OK com o HTML da agenda
```

**Observação:** o domínio e a resposta do servidor da Clínica Vida+ apresentados no diagrama são ilustrativos, pois o sistema ainda está em desenvolvimento.

## Evidência do DNS

Consulta local de `github.com`:

```text
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   github.com
Address: 140.82.113.4
```

Consulta usando o DNS do Google:

```text
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:   github.com
Address: 140.82.114.4
```

As consultas de `uni9.br` retornaram `NXDOMAIN` nos dois servidores DNS testados. Já o domínio `github.com` foi resolvido para endereços IP. O comando `ping` enviou quatro pacotes, mas não recebeu respostas. Isso não significa necessariamente que o GitHub esteja fora do ar, pois respostas de ping podem ser bloqueadas.

## Evidência do HTTP

| Recurso | Método | Status | Tipo |
|---|---|---|---|
| `https://github.com/` | GET | 200 | Documento HTML |
| `environments` | GET — método a confirmar | 403 | Fetch |
| `light-3a3d2c708970e4e8.css` | GET | 200 | CSS |
| `element-registry-dd61af1ba176ae38.js` | GET | 200 | JavaScript |

As requisições de documento, CSS e JavaScript foram observadas nas ferramentas de desenvolvedor do navegador ou nos arquivos HAR coletados. A requisição `environments` apresentou status `403` em uma captura anterior; seu método deve ser confirmado na requisição original antes da entrega final.

## Por que o formulário de agendamento precisa de HTTPS

O formulário da Clínica Vida+ pode receber dados pessoais sensíveis, como CPF, telefone e data de nascimento. Sem criptografia durante a transmissão, essas informações podem ser interceptadas e lidas por terceiros. O HTTPS usa TLS para proteger os dados em trânsito e ajuda a verificar a identidade do servidor. Por isso, o sistema deve utilizar HTTPS em todas as páginas, especialmente durante o envio do formulário de agendamento.