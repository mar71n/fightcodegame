# fightcodegame
fightcodegame.com lucha de robots javascript 

<h1> CÓMO ESCRIBIR MI ROBOT? </h1>
sigue leyendo para saber más sobre nuestra javascript API robótica

<h3>introducción</h3>
fightcodegame.com es un país libre para jugar juego y usted puede crear tantos robots como desee.
Codificación de los robots es muy fácil y debe ser natural si alguna vez has tenido alguna experiencia Javascript. Vamos a ver un ejemplo de un robot muy simple:

``` javascript
function Robot(robot) {}

// well, we need to do something...
// whenever our robot is idle, this method gets called.
// Así, tenemos que hacer algo ...
// Siempre que nuestro robot está inactivo, este método se llama.
Robot.prototype.onIdle = function(ev) {
    var robot;
    robot = ev.robot;
    robot.ahead(150);
    robot.rotateCannon(360);
    robot.back(100);
    robot.rotateCannon(360);
    robot.turn(20);
};

// this method gets called whenever we hit another robot...
// Este método es llamado cada vez que llegamos a otro robot ...
Robot.prototype.onRobotCollision = function(ev) {};

// this method gets called whenever we hit a wall...
// Este método es llamada cuando golpeamos una pared ...
Robot.prototype.onWallCollision = function(ev) {};

// yay we see another robot! time to wreak some havoc...
// Yay vemos otro robot! tiempo para causar algunos estragos ...
Robot.prototype.onScannedRobot = function(ev) {
    var robot;
    robot = ev.robot;
    robot.fire(1);
};

// ohhh... we were hit by another robot...
// Ohhh ... fuimos golpeados por otro robot ...
Robot.prototype.onHitByBullet = function(ev) {
    var robot;
    robot = ev.robot;
    robot.turn(90 - ev.bulletBearing);
};
```
Hay un montón de métodos de ahí que usted probablemente no entienda del todo todavía, pero vamos a ver más de ellos en un minuto.

<h3>Estructura Robot</h3>
Su robot es sólo una función constructora Javascript simple y llano. fightcode utilizará su función constructora para crear la instancia que va a luchar contra otros robots.
Este es el robot más simple posible que pueda construir (que no hace mucho, sin embargo):
```Robot function () {} Robot.prototype.onIdle = function (ev) {};```

<h3>Función OnIdle</h3>
La función OnIdle es su bucle del juego robot. fightcode llamará cada vez que su robot se queda sin acciones que hacer.
Usted puede usarlo para correr, girar, rodar o cualquier cosa que su robot puede hacer. Podemos ponerlo en práctica con:
``` javascript
function Robot() {}

Robot.prototype.onIdle = function(ev) {
    var robot = ev.robot;
    robot.turn(90); // keep turning 90 degrees when idle
};
```
OnIdle es una función que toma un argumento llamado ev. La estructura de ev es la siguiente:
``` javascript
{
    robot: {
        // INFORMATION ON THE CURRENT GAME
        id,                    // Id from your robot
        angle,                 // Current angle from your robot in degrees
        cannonRelativeAngle,   // Current angle from your cannon
                               // relative to your robot
        cannonAbsoluteAngle,   // Current angle from your cannon
                               // relative to the board
        position: {
            x,                 // X position in the board from your robot
            y                  // Y position in the board from your robot
        },
        life,                  // Percentage of the life from your robot
        gunCoolDownTime,       // Time remaining in the cooldown from your
                               // cannon after shooting
        availableClones,       // Number of available clones you can use
        availableDisappears,   // Number of available disappears you can use
        parentId,              // In the case of being a clone, the id
                               // from your parent element. null otherwise
        arenaWidth,            // Width from the board
        arenaHeight            // Height from the board

        // AVAILABLE ACTIONS

        // Moves the given amount ahead
        ahead: function(amount),

        // Moves the given amount backwards
        back: function(amount),

        // Moves ahead if direction equals to 1, backwards otherwise
        move: functon(amount, direction),

        // Rotates your cannon angle by the specified number of degrees
        rotateCannon: function(amount),

        // Turns the cannon gun left relative to the robot by the specified
        // number of degrees (equivalent to rotateCannon(-1 * amount))
        // Activa el arma de cañón a la izquierda en relación con el robot por el 
        // número de grados especificado (equivalente a rotar Cannon (-1 * cantidad))
        turnGunLeft: function(degrees),

        // Turns the cannon gun right relative to the robot by the specified
        // number of degrees (equivalent to rotateCannon(amount))
        turnGunRight: function(degrees),

        // Rotates your robot the by the specified number of degrees
        turn: function(degrees),

        // Rotates your robot to the left by the specified number of
        // degrees (equivalent to turn(-1 * degrees))
        turnLeft: function(degrees),

        // Rotates your robot to the right by the specified number of
        // degrees (equivalent to turn(degrees))
        turnRight: function(degrees),

        // Fires your cannon. This functin has a cooldown before you can
        // use it again.
        // Fires su cañón. Esta función tiene un tiempo de reutilización (cooldown)
        //  antes de poder utilizarlo de nuevo.
        fire: function(),

        // Subscribe to get notified whenever this action gets called
        // in the queue.
        // Suscríbete para ser notificado cada vez que esta acción se llama
        // En la cola.
        notify: function(callback),

        // Removes all remaining actions your robot has from the queue
        // Elimina todas las acciones restantes de su robot tiene de la cola
        stop: function(),

        // Clones your robot into another robot and can only be used once
        // per fight. Remember to check the parentId property to stop
        // your clone from shooting you.
        // Clones su robot en otro robot y sólo puede ser utilizado una vez 
        // por pelea. Recuerde revisar la propiedad parentId para detener 
        // su clon de dispararte.
        clone: function(),

        // Disappear your robot for 200 rounds, doing your robot undetected
        // by enemy robots (onScannedRobot the event is not triggered) and
        // can only be used once per fight.
        // Disappear su robot de 200 rondas, haciendo su robot indetectable
        // Por robots enemigos (onScannedRobot el evento no se activa) y
        // Sólo se puede utilizar una vez por combate.
        disappear: function(),

        // Logs message to the console.
        log: function(message),

        // Stops your robot from listening a given event (onWallColision,
        // for instance).
        // Detiene su robot de escuchar un determinado evento (onWallColision,
        // Por ejemplo).
        ignore: function(eventName),

        // Starts listening a given event (onWallColision, for instance).
        listen: function(eventName)
    }
}
```
La información disponible en este objeto es la base para todos los demás eventos. En los demás eventos sólo vamos a documentar sus propiedades adicionales.
Su función constructora recibe el "robot". Esto significa que en su constructor no es necesario asignar ev.robot a una variable.
