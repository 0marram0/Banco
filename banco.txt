<?php 

$opcoes = ["Criar um cliente", "Criar uma conta", "Depositar Saldo", "Sacar saldo", "Consultar saldo", "Esqueci meu ID da conta"];

$clientes = [];
$contas   = [];

function cadastrar_cliente(&$clientes, string $nome, string $cpf, string $telefone): void {
    
    //global $clientes; //Alternativa para acesso de variáveis fora do escopo da função

    $cliente = [
        "nome" => $nome,
        "cpf"  => $cpf, //11 digitos
        "telefone" => $telefone //10 digitos
    ];
    
    $clientes[] = $cliente;
    
}

function cadastrar_conta(&$contas, $cpfCliente): void {
    
    $conta = [
        "numeroConta" => uniqid(),
        "cpfCliente" => $cpfCliente,
        "saldo" => 0
    ];
    
    $contas[] = $conta;
}

function depositar(&$contas, $numeroConta, $quantia): void {

    foreach ($contas as &$conta) {

        if ($conta['numeroConta'] == $numeroConta) {

            $conta['saldo'] += $quantia;
            
            break;
        }
    }
}

function sacar(&$contas, $numeroConta, $quantia): void {
    
    foreach ($contas as &$conta) {

        if ($conta['numeroConta'] == $numeroConta) {

            $conta['saldo'] -= $quantia;

            break;

        }

    }
}

function consultar_saldo($contas, $numeroConta) {
    
    foreach ($contas as $conta) {

        if ($conta['numeroConta'] == $numeroConta) {

            $saldo = $conta['saldo'];

            break;
        }

    }

    return $saldo;
}

function fazerMenu($opcoes) {

    fazer_cabecalho("Menu");

    $posicao = 1;
    
    foreach ($opcoes as $opcao) {
        print("{$posicao} - {$opcao}" . "\n");
        $posicao++;
    }
}

function validarCPF(string $cpf_original = null): bool{

    if (strlen($cpf_original) < 11 or strlen($cpf_original) > 11) {
        return false;
    }

    $cpf_validado = substr($cpf_original, 0, 9); // Pegando os 9 primeiros caracteres do CPF

    $valor = 0;

    for ($i = 1; $i <= 9; $i++) {

        $posicao_numero = $i - 1;

        $multiplicador = 10 - ($i - 1 * 1); 
        
        $valor += $cpf_validado[$posicao_numero] * $multiplicador;
    }
    
    $resto = $valor % 11;

    // Reinicia as variáveis

    $valor = 0;

    $multiplicador = 0;

    $posicao_numero = 0;

    if ($resto < 2) {

        $digito1 = 0;

    }
    else {

        $digito1 = 11 - $resto;

    }

    $cpf_validado = $cpf_validado . $digito1;

    
    for ($i = 1; $i <= 10; $i++) {

        $posicao_numero = $i - 1;

        $multiplicador = 11 - ($i - 1 * 1); 
        
        $valor += $cpf_validado[$posicao_numero] * $multiplicador;

    }
    
    $resto = $valor % 11;
            
    if ($resto < 2) {

        $digito2 = 0;

    }
    else {
    
        $digito2 = 11 - $resto;
    }

    $cpf_validado = $cpf_validado . $digito2;
            
    if($cpf_validado != $cpf_original) {
        return false;
    }

    return true;
} 

function escolha_executada($clientes, $contas, $opcoes) {

    print("\n");

    $opcaoEscolhida = readline("Qual opção você quer executar? ");
    
    clear();

    switch ($opcaoEscolhida) {


        case 1:
            cabecalho_escolhas($opcoes, 1);

            $nome = readline("Entre com seu nome: ");
            if (voltar_menu($nome)) {
                break;
                clear();
            }

        
            do {
                
                $cpf = readline("Entre com seu CPF: ");
                
                if (voltar_menu($cpf)) {
                    break;
                    clear();
                }
                    
                else if (! (validarCPF($cpf)) ) {
                    print("\tEsse CPF é inexistente, tente novamente!\n");
                }
                
                else if (registro_cpf($clientes, $cpf)) {
                    print("\tEsse CPF já está registrado, tente novamente!\n");
                }

                else {
                    break;
                }

            } while (true);

            if (voltar_menu($cpf)) {
                break;
            }


            do {
                
                $telefone = readline("Entre com seu telefone: ");

                $telefone = preg_replace("/[^0-9]/", "", $telefone);
                
                if (voltar_menu($telefone)) {
                    break;
                    clear();
                }
                    
                else if (strlen($telefone) != 11) {
                    print("\tQuantidade de digitos inválida!\n");
                }
                else {
                    break;
                }
            } while (true);

            if (voltar_menu($telefone)) {
                break;
                clear();
            }


            cadastrar_cliente($clientes, $nome, $cpf, $telefone);

            break;



        case 2:

            cabecalho_escolhas($opcoes, 2);

            do {
                
                $cpf = readline("Entre com seu CPF: ");
                
                if (voltar_menu($cpf)) {
                    break;
                    clear();
                }

                else if (! (registro_cpf($clientes, $cpf))) {
                    print("\tEsse CPF não é um de nossos clientes, tente novamente!\n");
                }

                else if (registro_conta($contas, $cpf)) {
                    print("\tEsse CPF já possui uma conta!\n");
                }

                else {
                    break;
                }

            } while (true);

            if (voltar_menu($cpf)) {
                break;
            }


            cadastrar_conta($contas, $cpf);

            
            foreach ($contas as $conta) {
                if ($cpf == $conta["cpfCliente"]) {
                    print("Seu id é: {$conta["numeroConta"]}\n");
                }
            }

            $saida = readline("    Para sair dê enter");

            break;



        case 3: 

            cabecalho_escolhas($opcoes, 3);

            do {
                
                $id_cliente = readline("Entre com o ID da conta que queres depositar: ");
                
                if (voltar_menu($id_cliente)) {
                    break;
                    clear();
                }

                else if (! (registro_id($contas, $id_cliente))) {
                    print("\tEsse ID não existe, tente novamente!\n");
                }


                else {
                    break;
                }

            } while (true);
            
            if (voltar_menu($id_cliente)) {
                break;
            }


            do {
                
                $quantia = readline("Entre com a quantidade do depósito: ");
                
                if (voltar_menu($quantia)) {
                    break;
                    clear();
                }

                else if ($quantia <= 0 or ! is_numeric($quantia)) {
                    print("\tValor inválido, tente novamente!\n");
                }

                else {
                    break;
                }

            } while (true);
            
            if (voltar_menu($quantia)) {
                break;
            }

            depositar($contas, $id_cliente, $quantia);

            print("\n\t\033[32mValor depositado com sucesso!\033[m");
            
            break;

            sleep(3);


        case 4:

            cabecalho_escolhas($opcoes, 4);

            
            do {
                
                $id_cliente = readline("Entre com o ID da conta que queres sacar: ");
                
                if (voltar_menu($id_cliente)) {
                    break;
                    clear();
                }

                else if (! (registro_id($contas, $id_cliente))) {
                    print("\tEsse ID não existe, tente novamente!\n");
                }


                else {
                    break;
                }

            } while (true);
            
            if (voltar_menu($id_cliente)) {
                break;
            }


            do {
                
                $quantia = readline("Entre com a quantidade do saque: ");
                
                if (voltar_menu($quantia)) {
                    break;
                    clear();
                }

                else if ($quantia <= 0 or $quantia > consultar_saldo($contas, $id_cliente) or ! is_numeric($quantia)) {
                    print("\tValor inválido, tente novamente!\n");
                }

                else {
                    break;
                }

            } while (true);
            
            if (voltar_menu($quantia)) {
                break;
            }

            sacar($contas, $id_cliente, $quantia);

            print("\n\t\033[32mValor sacado com sucesso!\033[m");
            
            break;

            sleep(3);



        case 5:

            cabecalho_escolhas($opcoes, 5);

            do {
                
                $id_cliente = readline("Entre com o ID da conta que queres sacar: ");
                
                if (voltar_menu($id_cliente)) {
                    break;
                    clear();
                }

                else if (! (registro_id($contas, $id_cliente))) {
                    print("\tEsse ID não existe, tente novamente!\n");
                }


                else {
                    break;
                }

            } while (true);
            
            if (voltar_menu($id_cliente)) {
                break;
            }

            $saldo = consultar_saldo($contas, $id_cliente);
            
            printf("O saldo da sua conta é: %.2f", $saldo);

            sleep(3);

            break;
            

        case 6:

            cabecalho_escolhas($opcoes, 6);

            do {
                
                $cpf = readline("Entre com seu CPF: ");
                
                if (voltar_menu($cpf)) {
                    break;
                    clear();
                }
                
                
                else if (! (registro_cpf($clientes, $cpf))) {
                    print("\tEsse CPF não é um de nossos clientes, tente novamente!\n");
                }

                
                else if (! (registro_conta($contas, $cpf))) {
                    print("\tEsse CPF não possui conta, tente novamente!\n");
                }

                else {
                    break;
                }

            } while (true);

            
            if (voltar_menu($cpf)) {
                break;
            }

            
            
            do {
                
                $telefone = readline("Entre com seu telefone: ");

                $telefone = preg_replace("/[^0-9]/", "", $telefone);
                
                if (voltar_menu($telefone)) {
                    break;
                    clear();
                }
                
                else if (telefone_correspondente($clientes, $telefone, $cpf)) {
                    break;
                }
                else {
                    print("\tEsse telefone está incorreto, tente novamente!\n");
                }
            } while (true);

            if (voltar_menu($telefone)) {
                break;
            }


            foreach ($contas as $conta) {
                if ($cpf == $conta["cpfCliente"]) {
                    print("\nSeu id é: {$conta["numeroConta"]}\n");
                }
            }

            $saida = readline("\nPara sair dê enter");

            break;
            

    }

    sleep(2);
    
    clear();

    fazerMenu($opcoes);
    escolha_executada($clientes, $contas, $opcoes);
}

function registro_cpf($clientes, $cpf) {

    $cpf_registrado = false;

    foreach ($clientes as $cliente) {
        if ($cpf == $cliente["cpf"]) {
            $cpf_registrado = true;
        }
    }

    return $cpf_registrado;

}

function registro_conta($contas, $cpf) {

    $conta_registrada = false;

    foreach ($contas as $conta) {
        if ($cpf == $conta["cpfCliente"]) {
            $conta_registrada = true;
        }
    }

    return $conta_registrada;

}

function registro_id($contas, $id) {

    $id_registrado = false;

    foreach ($contas as $conta) {
        if ($id == $conta["numeroConta"]) {
            $id_registrado = true;
        }
    }

    return $id_registrado;

}

function telefone_correspondente($clientes, $telefone, $cpf) {

    $telefone_correto = false;

    foreach ($clientes as $cliente) {
        if ($cpf == $cliente["cpf"]) {
            if ($telefone == $cliente["telefone"]) {
                $telefone_correto = true;
            }
        }
    }

    return $telefone_correto;

}

function voltar_menu($valor): bool {
    if ($valor == null or $valor == "") {
        return true;
    }
    
    else {
        return false;
    }
}

function fazer_cabecalho($titulo): void {

    print(str_repeat("-=", 30) . "\n");
    print("\t* $titulo\n");
    print(str_repeat("-=", 30));
    print(str_repeat("\n", 2));
}

function cabecalho_escolhas($opcoes, $escolha): void {
    $posicao_escolha = $escolha - 1;

    fazer_cabecalho($opcoes[$posicao_escolha]);
}

function clear() {
    echo chr(27).chr(91).'H'.chr(27).chr(91).'J';
}

fazerMenu($opcoes);
escolha_executada($clientes, $contas, $opcoes);

