--This is the program that demonstrates the glitch in my game.

~~~
--# Main

function setup()
    
    Scene("Maze", MazeScene)
    Scene("Screen", Screen)
    Scene.Change("Maze")
    
    end


function draw()

    Scene.Draw()
    
end

function touched(touch)
    
    Scene.Touched(touch)
    
end

function pb(x,y,x1,y1)
    Scene.Pb(x,y,x1,y1)
end

function createmaze(n, sx, sy, ex, ey)
    Scene.CreateMaze(n, sx, sy, ex, ey)
end

function push(a, b)
    Scene.Push(a, b)
end

function pop()
    Scene.Pop()
end

function getneighbor(x, y, d)
    Scene.GetNeighbor(x, y, d)
end

function notvisited(x, y)
    Scene.NotVisited(x, y)
end

function setvisited(x, y)
    Scene.SetVisited(x, y)
end

function getunvisitedneighbor(x, y)
    Scene.GetUnvisitedNeighbor()
end

function getdirection(x, y, nx, ny)
    Scene.GetDirection(x,y,nx,ny)
end

function erasewalls(x, y, nx, ny)
    Scene.EraseWalls(x, y, nx, ny)
end

--# MazeScene

MazeScene = class()

function MazeScene:init(x)
    displayMode(FULLSCREEN)
  supportedOrientations(LANDSCAPE_ANY)
    c1=physics.body(CIRCLE, 15)
    c1.x=200
    c1.y=20
    c1.gravityScale=0
    c1.sleepingAllowed=false
    tab1={}
    tab2={}
    EAST = 0
    NORTH = 1
    WEST = 2
    SOUTH = 3
    EWALL = 1
    NWALL = 2
    WWALL = 4
    SWALL = 8
    XOFF = 30
    YOFF = 70
    d=0
    ALLWALLS = NWALL + EWALL + SWALL + WWALL 
    VISITED = 16
    NMAX = 15
    N = 10
    pN = 10
    M = WIDTH
    XYOFF = XOFF
    if HEIGHT < WIDTH then
        M = HEIGHT                
        XYOFF = YOFF
    end
    stack = {}
    startx = 1; starty = 1
    endx  = N; endy = N
    createmaze(N, startx, starty, endx, endy)

    --solvemaze()

end

-- This function gets called once every frame 
function MazeScene:draw() 
    -- This sets a dark background color 
    background(0,0,0)
    translate(0,100)
    strokeWidth(2)
    fill(255, 0, 0, 255)
    ellipse(c1.x, c1.y, 30)
    c1.x = c1.x+Gravity.x*15
    c1.y = c1.y+Gravity.y*15
    fill(255, 255, 255, 255)
    stroke(255, 255, 255, 255)
    for i=1, N do
        x = XOFF + (i-1)*S
        for j=1, N do
            y = YOFF + (j-1)*S
            c = maze[i][j]
            w = c%VISITED
            we = w%2
            wn = math.floor(w/NWALL)%2
            ww = math.floor(w/WWALL)%2
            ws = math.floor(w/SWALL)
            if we == 1 then
                line(x+S+150, y-50, x+S+150, y-S-50)
                pb(x+S+150,y-50,x+S+150,y-S-50)
            end
            if wn == 1 then
                line(x+150, y-50, x+S+150, y-50)
                pb(x+150, y-50, x+S+150, y-50)
            end
            if ww == 1 then
                line(x+150, y-50, x+150, y-S-50)
                pb(x+150, y-50, x+150, y-S-50)
            end
            if ws == 1 then
                line(x+150, y-S-50, x+S+150, y-S-50)
                pb(x+150, y-S-50, x+S+150, y-S-50)
            end
        end
    end
       done=true 
    if c1.x > 800 then
        Scene.Change("Screen")
    end
    
        end

    
    
  --print("x: "..CurrentTouch.x.." y: "..CurrentTouch.y)
 --   print("x: "..c1.x.." y: "..c1.y)
    

    
    function MazeScene:pb(x,y,x1,y1) 
    
    local z
    if not done then
        for z=1,#tab1 do
            if tab1[z].x==x and tab1[z].y==y and
                tab1[z].z==x1 and tab1[z].w==y1 then
                    return
            end
        end
        table.insert(tab1,vec4(x,y,x1,y1))
        table.insert(tab2,physics.body(EDGE,vec2(x,y),vec2(x1,y1)))
        tab2[#tab2].sleepingAllowed=false
    end
        end

function MazeScene:createmaze(n, sx, sy, ex, ey)
    
    if ex > N then
        ex = N; ey = N
    end
    S = math.floor((M - 2*XYOFF)/N)
    maze = {}
    for i=0, NMAX+1 do
        maze[i] = {}
        for j = 0, NMAX+1 do
            maze[i][j] = ALLWALLS
            if i == 0 or i == N+1 then
                maze[i][j] = maze[i][j] + VISITED
            elseif j == 0 or j == N+1 then
                maze[i][j] = maze[i][j] + VISITED
            end
        end
    end
    x = ex; y = ey
    sp = 1
    setvisited(ex, ey)
    erasewalls(sx-1, sy, sx, sy)
    erasewalls(ex, ey, ex+1, ey)      
    while true do
        nx, ny = getunvisitedneighbor(x, y)
        if nx < 0 then
            x, y = pop()
            if sp == 1 then          
                break
            end
        else 
            setvisited(nx, ny)
            push(x, y)
            erasewalls(x, y, nx, ny)
            x = nx; y = ny
        end
    end
        
        end 


function MazeScene:push(a, b)
    stack[sp]  = a
    stack[sp+1] = b
    sp = sp + 2
end

function pop()
    sp = sp - 1
    b = stack[sp]
    a = stack[sp-1]
    sp = sp - 1
    return a, b
end

function getneighbor(x, y, d)
    --print("x ", x, "y ", y, "d ", d)
    if d == EAST then
        x = x + 1
    elseif d == NORTH then
        y = y + 1
    elseif d == WEST then
        x = x -1
    else 
        y = y - 1
    end
    return x, y
end

function notvisited(x, y)
    if x < 1 or x > N then
        return false
    end
    if y < 1 or y > N then
        return false
    end
    v = math.floor(maze[x][y] / VISITED)
    if v >= 1 then
        return false
    else 
        return true
    end
end

function MazeScene:setvisited(x, y)
    maze[x][y] = maze[x][y] + VISITED
end

function getunvisitedneighbor(x, y)
    nu = 0
    uds = {}
    for i=EAST, SOUTH do
        nx, ny = getneighbor(x, y, i)
        if notvisited(nx, ny)then
            nu = nu + 1
            uds[nu] = i
        end
    end
    if nu == 0 then
        return -1, -1
    end
    td = math.random(1, nu)
    nx, ny = getneighbor(x, y, uds[td])
    return nx, ny
end

function getdirection(x, y, nx, ny)
    if nx > x then
        d = EAST
    elseif ny > y then
        d = NORTH
    elseif nx < x then
        d = WEST
    else
        d = SOUTH
    end
    return d
end

function MazeScene:erasewalls(x, y, nx, ny)
    d = getdirection(x, y, nx, ny)
    if d == EAST then
        maze[x][y] = maze[x][y] - EWALL
        maze[nx][ny] = maze[nx][ny] - WWALL
    elseif d == NORTH then
        maze[x][y] = maze[x][y] - NWALL
        maze[nx][ny] = maze[nx][ny] - SWALL
    elseif d == WEST then
        maze[x][y] = maze[x][y] - WWALL
        maze[nx][ny] = maze[nx][ny] - EWALL
    else
        maze[x][y] = maze[x][y] - SWALL
        maze[nx][ny] = maze[nx][ny] - NWALL
    end
end

--# Screen
Screen = class()
local backButton
function Screen:init(x)
    -- you can accept and set parameters here
    backButton = Button("Cargo Bot:Command Left", vec2(512, 384))
end

function Screen:draw()
    -- Codea does not automatically call this method
    background(0, 255, 59, 255)
    fontSize(30)
    fill(0, 2, 255, 255)
    text("This button goes back to the maze scene.", 512, 430)
    backButton:draw()
end

function Screen:touched(touch)
    -- Codea does not automatically call this method
    backButton:touched(touch)
    if backButton.selected then
        Scene.Change("Maze")
    end
end

--# HelperClass
-- HelperClass

Button = class()

function Button:init(buttonImage, buttonPosition)
    -- accepts the button image and location to draw it
    
    self.buttonImage = buttonImage
    self.buttonLocation = buttonPosition
    
    self.buttonTouchScale = 1.15
    self.buttonImageSize = vec2(spriteSize(self.buttonImage))    
    self.currentButtonImage = self.buttonImage
    self.buttonTouchedImage = resizeImage(self.buttonImage, (self.buttonImageSize.x*self.buttonTouchScale), (self.buttonImageSize.y*self.buttonTouchScale))   
    self.selected = false
end

function Button:draw()
    -- Codea does not automatically call this method
 
    pushStyle()   
    pushMatrix()
    noFill()
    noSmooth()
    noStroke()
     
    sprite(self.currentButtonImage, self.buttonLocation.x, self.buttonLocation.y)
    
    popMatrix()
    popStyle()
end

function Button:touched(touch)   
    -- local varaibles
    local currentTouchPosition = vec2(touch.x, touch.y)
    
    -- reset touching variable to false
    self.selected = false
    
    if (touch.state == BEGAN) then
         if( (self.buttonLocation.x - self.buttonImageSize.x/2) < currentTouchPosition.x and
            (self.buttonLocation.x + self.buttonImageSize.x/2) > currentTouchPosition.x and
            (self.buttonLocation.y - self.buttonImageSize.y/2) < currentTouchPosition.y and
            (self.buttonLocation.y + self.buttonImageSize.y/2) > currentTouchPosition.y ) then
                
            self.currentButtonImage = self.buttonTouchedImage
            --print("Now touching! - began")
        else          
            self.currentButtonImage = self.buttonImage  
            --print("Not touching - began")
        end             
    end
    
    if (touch.state == MOVING) then
        if( (self.buttonLocation.x - self.buttonImageSize.x/2) < currentTouchPosition.x and
            (self.buttonLocation.x + self.buttonImageSize.x/2) > currentTouchPosition.x and
            (self.buttonLocation.y - self.buttonImageSize.y/2) < currentTouchPosition.y and
            (self.buttonLocation.y + self.buttonImageSize.y/2) > currentTouchPosition.y ) then
        
            self.currentButtonImage = self.buttonTouchedImage
            --print("Now touching! - moving")
        else
            self.currentButtonImage = self.buttonImage  
            --print("Not touching - moving")
        end
    end
    
    if (touch.state == ENDED) then
        if( (self.buttonLocation.x - self.buttonImageSize.x/2) < currentTouchPosition.x and
            (self.buttonLocation.x + self.buttonImageSize.x/2) > currentTouchPosition.x and
            (self.buttonLocation.y - self.buttonImageSize.y/2) < currentTouchPosition.y and
            (self.buttonLocation.y + self.buttonImageSize.y/2) > currentTouchPosition.y ) then
        
            self.selected = true
            --print("Activated button")
        end
         
        self.currentButtonImage = self.buttonImage
    end 
end

function resizeImage(img, width, height)
    -- function from
    -- http://codea.io/talk/discussion/3490/importing-pics-from-dropbox/p1
    
    local newImg = image(width,height)
    setContext(newImg)
    sprite( img, width/2, height/2, width, height )    
    setContext()
    return newImg
end


-- Dragging Object Class
-- This class simplifies the process of dragging and dropping objects
-- You can have several objects interacting, but it is not mulit-touch
--
-- Parameters to pass in:
--  1. Object image name
--  2. A vector2 that is the location of the button
--  3. Optional object id 


SpriteObject = class()

function SpriteObject:init(objectImage, objectStartPosition, objectID) 

    self.objectImage = objectImage
    self.objectStartLocation = objectStartPosition
    self.ID = objectID or math.random()
    
    self.objectCurrentLocation = self.objectStartLocation
    self.objectImageSize = vec2(spriteSize(self.objectImage))
    self.selected = false
    self.dragOffset = vec2(0,0)
    self.draggable = true
  
    DRAGGING_OBJECT_MOVING = nil or DRAGGING_OBJECT_MOVING    
end

function SpriteObject:draw()
    -- Codea does not automatically call this method
    
    pushStyle()   
    pushMatrix()
    noFill()
    noSmooth()
    noStroke()
     
    sprite(self.objectImage, self.objectCurrentLocation.x, self.objectCurrentLocation.y)
     
    popMatrix()
    popStyle()

end

function SpriteObject:touched(touch)
    -- Codea does not automatically call this method
    
    -- local varaibles
    local currentTouchPosition = vec2(touch.x, touch.y)
    
    -- reset touching variable to false
    self.selected = false
    
    if (touch.state == BEGAN and self.draggable == true) then
        if( (self.objectCurrentLocation.x - self.objectImageSize.x/2) < currentTouchPosition.x and
            (self.objectCurrentLocation.x + self.objectImageSize.x/2) > currentTouchPosition.x and
            (self.objectCurrentLocation.y - self.objectImageSize.y/2) < currentTouchPosition.y and
            (self.objectCurrentLocation.y + self.objectImageSize.y/2) > currentTouchPosition.y ) then
            -- if the touch has began, we need to find delta from touch to center of object
            -- since will need it to reposition the object for draw
            -- subtracting 2 vec2s here
            self.dragOffset = self.objectCurrentLocation - currentTouchPosition
            DRAGGING_OBJECT_MOVING = self.ID
        end        
    end
    
    if (touch.state == MOVING and self.draggable == true) then
        if( (self.objectCurrentLocation.x - self.objectImageSize.x/2) < currentTouchPosition.x and
            (self.objectCurrentLocation.x + self.objectImageSize.x/2) > currentTouchPosition.x and
            (self.objectCurrentLocation.y - self.objectImageSize.y/2) < currentTouchPosition.y and
            (self.objectCurrentLocation.y + self.objectImageSize.y/2) > currentTouchPosition.y ) then
                -- only let it move if self.draggable == true
            if (self.draggable == true) then
                -- add the offset back in for its new position
                if (self.ID == DRAGGING_OBJECT_MOVING) then
                    self.objectCurrentLocation = currentTouchPosition + self.dragOffset
                end
            end
        end      
    end
    
    if (touch.state == ENDED and self.draggable == true) then
        DRAGGING_OBJECT_MOVING = nil   
    end
    
    -- this checks for if you have just touched the image
    -- you will have to release and re-touch for this to be activated again
    if (touch.state == BEGAN) then
        if( (self.objectCurrentLocation.x - self.objectCurrentLocation.x/2) < currentTouchPosition.x and
            (self.objectCurrentLocation.x + self.objectCurrentLocation.x/2) > currentTouchPosition.x and
            (self.objectCurrentLocation.y - self.objectCurrentLocation.y/2) < currentTouchPosition.y and
            (self.objectCurrentLocation.y + self.objectCurrentLocation.y/2) > currentTouchPosition.y ) then
        
            self.selected = true
            --print("Activated button")
        end
    end
end

function SpriteObject:isTouching(otherSpriteObject)
    -- this method checks if one dragging object is touching another dragging object
    
    local isItTouching = false
    
    if( (self.objectCurrentLocation.x + self.objectImageSize.x/2) > (otherSpriteObject.objectCurrentLocation.x - otherSpriteObject.objectImageSize.x/2) and
        (self.objectCurrentLocation.x - self.objectImageSize.x/2) < (otherSpriteObject.objectCurrentLocation.x + otherSpriteObject.objectImageSize.x/2) and
        (self.objectCurrentLocation.y - self.objectImageSize.y/2) < (otherSpriteObject.objectCurrentLocation.y + otherSpriteObject.objectImageSize.y/2) and
        (self.objectCurrentLocation.y + self.objectImageSize.y/2) > (otherSpriteObject.objectCurrentLocation.y - otherSpriteObject.objectImageSize.y/2) ) then
        -- if true, then not touching
        isItTouching = true
    end        
    
    return isItTouching
end


-- SceneManager
--
-- This file lets you easily manage different scenes
--     Original code from Brainfox, off the Codea forums

Scene = {}
local scenes = {}
local sceneNames = {}
local currentScene = nil

setmetatable(Scene,{__call = function(_,name,cls)
   if (not currentScene) then
       currentScene = 1
   end
   table.insert(scenes,cls)
   sceneNames[name] = #scenes
   Scene_Select = nil
end})

--Change scene
Scene.Change = function(name)
  currentScene = sceneNames[name]
    scenes[currentScene]:init()
   if (Scene_Select) then
       Scene_Select = currentScene
   end
    
   collectgarbage()
end

Scene.Draw = function()
   pushStyle()
   pushMatrix()
   scenes[currentScene]:draw()
   popMatrix()
   popStyle()
end

Scene.Touched = function(t)
   if (scenes[currentScene].touched) then
       scenes[currentScene]:touched(t)
   end
end

Scene.Keyboard = function()
   if (scenes[currentScene].keyboard) then
       scenes[currentScene]:keyboard(key)
   end
end

Scene.OrientationChanged = function()
   if (scenes[currentScene].orientationChanged) then
       scenes[currentScene]:orientationChanged()
   end
    end

Scene.CreateMaze = function(n, sx, sy, ex, ey)
        if (scenes[currentScene].createmaze) then
            scenes[currentScene]:createmaze(n, sx, sy, ex, ey)
        end
    end
Scene.Push = function(a, b)
    if (scenes[currentScene].push) then
        scenes[currentScene]:push(a, b)
    end
end
Scene.Pop = function()
    if (scenes[currentScene].pop) then
        scenes[currentScene]:pop()
    end
end
Scene.GetNeighbor = function(x,y,d)
    if (scenes[currentScene].getneighbor) then
        scenes[currentScene]:getneighbor(x,y,d)
    end
end
Scene.NotVisited = function(x,y)
    if (scenes[currentScene].notvisited) then
        scenes[currentScene]:notvisited(x, y)
    end
end
Scene.SetVisited = function(x, y)
    if (scenes[currentScene].setvisited) then
        scenes[currentScene]:setvisited(x, y)
    end
end
Scene.GetUnvisitedNeighbor = function(x, y)
    if (scenes[currentScene].getunvisitedneighbor) then
        scenes[currentScene]:getunvisitedneighbor(x, y)
    end
end

Scene.Pb = function(x, y, x1, y1)
    if (scenes[currentScene].pb) then
        scenes[currentScene]:pb(x, y, x1, y1)
    end
end
Scene.GetDirection = function(x,y,nx,ny)
    if (scenes[currentScene].getdirection) then
        scenes[currentScene]:getdirection(x,y,nx,ny)
    end
end

Scene.EraseWalls = function(x, y, nx, ny)
    if (scenes[currentScene].erasewalls) then
        scenes[currentScene]:erasewalls(x, y, nx, ny)
    end
end


