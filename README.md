# TicTacProlog
## Cómo correr la aplicación
Utilizando SWI-Prolog, abre el archivo usando consult.
Corre el siguiente comando
```
start.
```
## Documentación
### UI
Para el UI, se utiliza la librería xpce, tabular para hacer una tabla más eficientemente y autowin para modificar el tamaño de la ventana automáticamente.
```
:- use_module(library(pce)).
:- use_module(library(tabular)).
:- use_module(library(autowin)).
```
Se crea el display de la tabla en el predicado `make_table` y se conecta con el resto de los predicados de lógica.
Cada `send` manda datos al UI, como la creación de los botones y de sus respectivos labels.
```
make_table :-
    new(P, auto_sized_picture('Tic tac toe')),
    send(P, display, new(T, tabular)),
    send(T, border, 1),
    send(T, cell_spacing, -1),
    send(T, rules, all),
    send_list(T, [
        append(new(Button1, button(''))),
        append(new(Button2, button(''))),
        append(new(Button3, button(''))),
        next_row,
        append(new(Button4, button(''))),
        append(new(Button5, button(''))),
        append(new(Button6, button(''))),
        next_row,
        append(new(Button7, button(''))),
        append(new(Button8, button(''))),
        append(new(Button9, button('')))
    ]),

    send(Button1, message, message(@prolog, button_clicked, 1, Button1)),
    send(Button2, message, message(@prolog, button_clicked, 2, Button2)),
    send(Button3, message, message(@prolog, button_clicked, 3, Button3)),
    send(Button4, message, message(@prolog, button_clicked, 4, Button4)),
    send(Button5, message, message(@prolog, button_clicked, 5, Button5)),
    send(Button6, message, message(@prolog, button_clicked, 6, Button6)),
    send(Button7, message, message(@prolog, button_clicked, 7, Button7)),
    send(Button8, message, message(@prolog, button_clicked, 8, Button8)),
    send(Button9, message, message(@prolog, button_clicked, 9, Button9)),

    retractall(button_mapping(_)),
    asserta(button_mapping([Button1, Button2, Button3, Button4, Button5, Button6, Button7, Button8, Button9])),

    send(P, open).
```
## Explicación del código
#### Importar las librerías necesarias
```
:- use_module(library(pce)).
:- use_module(library(tabular)).
:- use_module(library(autowin)).
```
El primer paso es importar las librerías que se utilizan para el UI.

#### Definición del cuadrado mágico
```
magic_square([2, 7, 6, 
              9, 5, 1, 
              4, 3, 8]).
```
El segundo paso es implementar un cuadrado mágico. 
La suma de todas sus columnas, filas y diagonales da 15. 
Se utilizará para las condición de éxito.

#### Estado inicial
```
initial_board([_,_,_,
              _,_,_,
              _,_,_]).
```
El estado inicial es una lista completamente vacía que representa al tablero.

#### Revisar si el tablero está lleno
```
board_full(Board) :-
    \+ member(_, Board).
```
Esta sentencia revisa que no haya variables libres en el tablero.
Si hay aunque sea una variable libre, significa que el tablero no está lleno, por lo que regresa falso.

#### Sumar la lista 
```
sum_list([], 0).
sum_list([H|T], Sum) :- sum_list(T, Rest), Sum is H + Rest.
```
Esta es una función recursiva. Infiere que la suma total de elementos en la lista será la cabeza más el resto.
Cuando no quedan elementos en la lista, la recursión termina. Entonces se combinan los resultados y se encuentra el total.

#### Combinación ganadora
```
winning_combination(Positions) :-
    magic_square(Square),
    findall(Value, (member(Pos, Positions), nth1(Pos, Square, Value)), Values),
    sum_list(Values, 15).
```
Esta función recibe una lista de posiciones. Después manda a llamar el cuadro mágico.
Suma las posiciones utilizando el cuadro mágico. 
En caso de que sea una combinación ganadora, la suma será de 15.

#### Revisar si jugador actual ganó
```
winner(Board, Player) :-
    WinningPositions = [
        [1, 2, 3], [4, 5, 6], [7, 8, 9],  % Rows
        [1, 4, 7], [2, 5, 8], [3, 6, 9],  % Columns
        [1, 5, 9], [3, 5, 7]              % Diagonals
    ],
    member(Pos, WinningPositions),
    nth1(1, Pos, P1), nth1(2, Pos, P2), nth1(3, Pos, P3),
    nth1(P1, Board, Mark1), nth1(P2, Board, Mark2), nth1(P3, Board, Mark3),
    nonvar(Mark1), nonvar(Mark2), nonvar(Mark3), % Ensure all marks are bound
    Mark1 = Mark2, Mark2 = Mark3, Mark3 = Player.
```
Esta función recibe la marca (o, x) que está usando el jugador actual y el tablero.
Al principio se definen las posibles combinaciones con las que se puede ganar.
Se comparan las marcas el tablero con cada una de las posiciones ganadoras. 
Si las 3 marcas en las 3 posiciones ganadoras son idénticas a la del jugador actual, significa que ganó.
Regresa verdadero si el jugador actual ganó.

#### Hacer movimiento
```
make_move(Board, Position, Player, NewBoard) :-
    nth1(Position, Board, _, Rest),
    nth1(Position, NewBoard, Player, Rest).
```
Se definió esta función para permitir que el jugador actual realice un movimiento.
Genera un tablero nuevo tablero en el que se agrega la marca actual (x,o)

### Representación del juego
#### Estado inicial


#### Estado final
        

### Lógica del juego


#### Movimientos válidos
Como jugador, el movimiento que puedes realizar es insertar una x en una casilla vacía presionando un botón.


## Test Cases
