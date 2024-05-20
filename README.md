# Integração API - Envio de Dados para Modal de Simulação de Crédito
Descrição
Este endpoint redireciona o usuário para a URL https://www.b2cap.com.br, com parâmetros específicos para pré-carregar um modal de simulação de crédito. O modal é exibido com os dados já preenchidos, permitindo que o cliente apenas verifique e confirme a submissão.

# Endpoint
```bash
GET https://www.b2cap.com.br?openModal=true
```
# Parâmetros
Os dados do cliente devem ser passados como parâmetros na URL. Apenas os valores dos parâmetros devem ser criptografados (e não as chaves). Os parâmetros incluem:

```json
{
  "simulacaoTipo": "carro", // carro, moto, caminhao ou onibus
  "localCompra": "loja", // loja, pessoa_fisica, leilao ou nao_sei
  "nomeCompleto": "FELLIPE JOSE DA SILVA",
  "nomeMaeCompleto": "LUCIA",
  "cpf": "705.444.55-01",
  "rg": "1231231",
  "email": "fellipejose2009@hotmail.com",
  "endComercial": "A",
  "endResidencial": "A",
  "telefone": "(32) 42432-4234",
  "dataNascimento": "23/03/1999",
  "cep": "23423-423",
  "leiloeiro": "",
  "dataLeilao": "",
  "numeroLote": "",
  "veiculo": "",
  "modeloLeilao": "",
  "anoCarro": "2023",
  "modeloCarro": "MODELO 1",
  "marcaCarro": "MARCA 1",
  "valorCarro": "2023",
  "blindado": "nao", // sim ou nao
  "termsCheckbox": "on"
}
```

# Criptografia dos Dados
A criptografia dos valores dos parâmetros é realizada utilizando a classe Encryptor abaixo. Use o método encode para criptografar os valores antes de passá-los na URL.

```php
class Encryptor
{
    const ALPHABET = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    const ENCODING = "MOhqm0PnycUZeLdK8YvDCgNfb7FJtiHT52BrxoAkas9RWlXpEujSGI64VzQ31w";

    public static function encode($text)
    {
        $map = array_combine(str_split(self::ALPHABET), str_split(self::ENCODING));
        $encodedText = '';
        foreach (str_split($text) as $char) {
            $encodedText .= $map[$char] ?? $char;
        }
        return $encodedText;
    }

    public static function decode($text)
    {
        $map = array_combine(str_split(self::ENCODING), str_split(self::ALPHABET));
        $decodedText = '';
        foreach (str_split($text) as $char) {
            $decodedText .= $map[$char] ?? $char;
        }
        return $decodedText;
    }
}

// Exemplo de uso
echo Encryptor::encode('Hello123');  // Saída: texto codificado
echo "\n";
echo Encryptor::decode('OYuu07');  // Saída: texto decodificado
```
# Nota
Garanta que todos os valores dos parâmetros estejam corretamente criptografados para evitar a exposição de dados sensíveis.


# Exemplo para gerar a URL com os dados:
```php
<?php

class Encryptor {
    const ALPHABET = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    const ENCODING = "MOhqm0PnycUZeLdK8YvDCgNfb7FJtiHT52BrxoAkas9RWlXpEujSGI64VzQ31w";

    public static function encode($text) {
        $map = [];
        // Criar o mapa de criptografia de ALPHABET para ENCODING
        for ($i = 0; $i < strlen(self::ALPHABET); $i++) {
            $map[self::ALPHABET[$i]] = self::ENCODING[$i];
        }

        $encodedText = '';
        // Codificar cada caractere do texto
        for ($i = 0; $i < strlen($text); $i++) {
            $char = $text[$i];
            if (array_key_exists($char, $map)) {
                $encodedText .= $map[$char];
            } else {
                $encodedText .= rawurlencode($char); // Usar rawurlencode para caracteres especiais
            }
        }

        return $encodedText;
    }
}

// Dados para codificar e adicionar à URL
$data = [
    'simulacaoTipo' => "carro",
    'localCompra' => "loja",
    'nomeCompleto' => "FELLIPE JOSE DA SILVA",
    'nomeMaeCompleto' => "LUCIA",
    'cpf' => "705.444.55-01",
    'rg' => "1231231",
    'email' => "fellipejose2009@hotmail.com",
    'endComercial' => "A",
    'endResidencial' => "A",
    'telefone' => "(32) 42432-4234",
    'dataNascimento' => "23/03/1999",
    'cep' => "23423-423",
    'leiloeiro' => "",
    'dataLeilao' => "",
    'numeroLote' => "",
    'veiculo' => "",
    'modeloLeilao' => "",
    'anoCarro' => "2023",
    'modeloCarro' => "MODELO 1",
    'marcaCarro' => "MARCA 1",
    'valorCarro' => "2023",
    'blindado' => "nao",
    'termsCheckbox' => "on"
];

// Criar a URL com os valores codificados
$baseURL = 'https://www.b2cap.com.br/?';
$queryParams = [];
foreach ($data as $key => $value) {
    if ($value !== "") {  // Ignorar campos vazios
        $queryParams[] = $key . '=' . Encryptor::encode($value);
    }
}
$encodedURL = $baseURL . implode('&', $queryParams);

echo $encodedURL;
?>
```

