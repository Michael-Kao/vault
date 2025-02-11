---
title: CG LightHouse tuto

---

# CG LightHouse tuto

## Before moving camera
```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

float red = 1.0f, blue = 1.0f, green = 1.0f; //所有變數值設為1表示圖形最初為白色

float angle = 0.0f; // angle of rotation for the camera direction
float lx = 0.0f, lz = -1.0f; // actual vector representing the camera's direction
float x = 0.0f, z = 5.0f; //XZ position of the camera

void renderScene(void)
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glLoadIdentity();
	gluLookAt(	0.0f, 0.0f, 10.0f,
				0.0f, 0.0f,  0.0f,
				0.0f, 1.0f,  0.0f
	); //設定相機
	glRotatef(angle, 0.0f, 1.0f, 0.0f);
	glColor3f(red, green, blue); // responsible for setting color
	glBegin(GL_TRIANGLES);
			glVertex3f(-2, -2, -5.0);//相機預設照往z方向的負值，所以z軸值為負才能出現在畫面上
			glVertex3f(2, 0.0, -5.0);
			glVertex3f(0.0, 2, -5.0);
	glEnd();
	angle += 0.1f;
	glutSwapBuffers();

}

void drawSnowMan()
{
	glColor3f(1.0f, 1.0f, 1.0f); // 所有值都為1.0表示圖形為白色
	//Draw body
	glTranslatef(0.0f, 0.75f, 0.0f);
	glutSolidSphere(0.75f, 20, 20);
	//Draw head
	glTranslatef(0.0f, 1.0f, 0.0f);
	glutSolidSphere(0.25f, 20, 20);
	//Draw Eyes
	glPushMatrix();
	glColor3f(0.0f, 0.0f, 0.0f);
	glTranslatef(0.0f, 0.10f, 0.18f);
	glutSolidSphere(0.05f, 10, 10);
	glTranslatef(-0.1f, 0.0f, 0.0f);
	glutSolidSphere(0.05f, 10, 10);
	glPopMatrix();
	//Draw Nose
	glColor3f(1.0f, 0.5f, 0.5f);
	glutSolidCone(0.08, 0.5f, 10, 2);
}

void changeSize(int w, int h)
{
	//prevent a divide by zero, when window is too short
	//you can't make a window of zero width
	if (h == 0)
		h = 1;
	float ratio = 1.0 * w / h;
	
	//use the projection matrix
	glMatrixMode(GL_PROJECTION);
	// reset matrix
	glLoadIdentity();
	//set the viewport to be the entire window
	glViewport(0, 0, w, h);
	//set the correct perspective
	gluPerspective(45, ratio, 1, 1000); //
	//get back to the modelview
	glMatrixMode(GL_MODELVIEW);
}

void processNormalKeys(unsigned char key, int x, int y)
{
	if (key == 27)
		exit(0);
}

void processSpecialKeys(int key, int x, int y)
{
	int mod;
	switch (key)
	{
		case GLUT_KEY_F1 : 
			mod = glutGetModifiers();
			if (mod == (GLUT_ACTIVE_CTRL | GLUT_ACTIVE_ALT))
			{
				red = 1.0;
				green = 0.0;
				blue = 0.0;
			}
			break;
		case GLUT_KEY_F2 :
			red = 0.0;
			green = 1.0;
			blue = 0.0;
			break;
		case GLUT_KEY_F3 :
			red = 0.0;
			green = 0.0;
			blue = 1.0;
			break;
		//case GLUT_KEY_UP :
			
	}
}

int main(int argc, char** argv)
{
	//init GLUT and create Window
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_DEPTH | GLUT_DOUBLE | GLUT_RGBA);
	glutInitWindowPosition(100, 100);
	glutInitWindowSize(320, 320);
	glutCreateWindow("My_CGTest");
	//register callbacks
	glutDisplayFunc(renderScene);// register callbacks
	glutReshapeFunc(changeSize);//
	glutIdleFunc(renderScene);
	glutKeyboardFunc(processNormalKeys);
	glutSpecialFunc(processSpecialKeys);
	glutMainLoop();//GLUT event processing cycle
	return 1;
}
```

Moving camera
```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

float red = 1.0f, blue = 1.0f, green = 1.0f; //所有變數值設為1表示圖形最初為白色

float angle = 0.0f; // angle of rotation for the camera direction
float lx = 0.0f, lz = -1.0f; // actual vector representing the camera's direction
float x = 0.0f, z = 5.0f; //XZ position of the camera

void drawSnowMan()
{
	glColor3f(1.0f, 1.0f, 1.0f); // 所有值都為1.0表示圖形為白色
	//Draw body
	glTranslatef(0.0f, 0.75f, 0.0f);
	glutSolidSphere(0.75f, 20, 20);
	//Draw head
	glTranslatef(0.0f, 1.0f, 0.0f);
	glutSolidSphere(0.25f, 20, 20);
	//Draw Eyes
	glPushMatrix();
	glColor3f(0.0f, 0.0f, 0.0f);
	glTranslatef(0.0f, 0.10f, 0.18f);
	glutSolidSphere(0.05f, 10, 10);
	glTranslatef(-0.1f, 0.0f, 0.0f);
	glutSolidSphere(0.05f, 10, 10);
	glPopMatrix();
	//Draw Nose
	glColor3f(1.0f, 0.5f, 0.5f);
	glutSolidCone(0.08, 0.5f, 10, 2);
}

void renderScene(void)
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glLoadIdentity();
	gluLookAt(	x, 1.0f, z, //camera position
				x + lx, 1.0f,  z + lz, //the point we are looking at,  Look At Point = Line Of Sight + Camera Position
				0.0f, 1.0f,  0.0f  //the up vector, usually set to (0.0, 1.0, 0.0), meaning that camera's is not tilted. upside down can use (0.0, -1.0, 0.0)
	); //設定相機
	//glRotatef(angle, 0.0f, 1.0f, 0.0f);
	// Draw ground
	glColor3f(0.9f, 0.9f, 0.9f); // responsible for setting color
	glBegin(GL_QUADS);
			glVertex3f(-100.0f, 0.0f, -100.0f);//相機預設照往z方向的負值，所以z軸值為負才能出現在畫面上
			glVertex3f(-100.0f, 0.0f,  100.0f);
			glVertex3f( 100.0f, 0.0f,  100.0f);
			glVertex3f( 100.0f, 0.0f, -100.0f);
	glEnd();
	// Draw 36 snowmen
	for (int i = -3; i < 3; i++)
	{
		for (int j = -3; j < 3; j++)
		{
			glPushMatrix();
			glTranslatef(i * 10.0f, 0, j * 10.0f);
			drawSnowMan();
			glPopMatrix();
		}
	}
	//angle += 0.1f;
	glutSwapBuffers();

}

void changeSize(int w, int h)
{
	//prevent a divide by zero, when window is too short
	//you can't make a window of zero width
	if (h == 0)
		h = 1;
	float ratio = 1.0 * w / h;
	
	//use the projection matrix
	glMatrixMode(GL_PROJECTION);
	// reset matrix
	glLoadIdentity();
	//set the viewport to be the entire window
	glViewport(0, 0, w, h);
	//set the correct perspective
	gluPerspective(45, ratio, 1, 1000); //
	//get back to the modelview
	glMatrixMode(GL_MODELVIEW);
}

void processNormalKeys(unsigned char key, int x, int y)
{
	if (key == 27)
		exit(0);
}

void processSpecialKeys(int key, int x, int y)
{
	int mod;
	float fraction = 0.1f;
	switch (key)
	{
		case GLUT_KEY_F1 : 
			mod = glutGetModifiers();
			if (mod == (GLUT_ACTIVE_CTRL | GLUT_ACTIVE_ALT))
			{
				red = 1.0;
				green = 0.0;
				blue = 0.0;
			}
			break;
		case GLUT_KEY_F2 :
			red = 0.0;
			green = 1.0;
			blue = 0.0;
			break;
		case GLUT_KEY_F3 :
			red = 0.0;
			green = 0.0;
			blue = 1.0;
			break;
		case GLUT_KEY_LEFT :
			angle -= 0.01f;
			lx = sin(angle);
			lz = -cos(angle);
			break;
		case GLUT_KEY_RIGHT :
			angle += 0.01f;
			lx = sin(angle);
			lz = -cos(angle);
			break;
		case GLUT_KEY_UP :
			x += lx * fraction;
			z += lz * fraction;
			break;
		case GLUT_KEY_DOWN :
			x -= lx * fraction;
			z -= lz * fraction;
			break;
	}
}

int main(int argc, char** argv)
{
	//init GLUT and create Window
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_DEPTH | GLUT_DOUBLE | GLUT_RGBA);
	glutInitWindowPosition(100, 100);
	glutInitWindowSize(320, 320);
	glutCreateWindow("My_CGTest");
	//register callbacks
	glutDisplayFunc(renderScene);// register callbacks
	glutReshapeFunc(changeSize);//
	glutIdleFunc(renderScene);
	glutKeyboardFunc(processNormalKeys);
	glutSpecialFunc(processSpecialKeys);
	// OpenGL init
	glEnable(GL_DEPTH_TEST);
	glutMainLoop();//GLUT event processing cycle
	return 1;
}
```

```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

// Window's width and height
int width = 800;
int height = 600;
const int MAX = 50; 
int shapeCount = 0;
float point_size = 1.0;
float line_width = 1.0;
bool dragging = false;

struct Color
{
    float r = 0.0f;
    float g = 0.0f;
    float b = 0.0f;
};

struct Shape
{
    string type;
    vector< pair< float, pair< float, Color > > > pnt; // the coordinate and color of each shape
    float pnt_size = 1.0;
    float lineWidth = 1.0;
    int limit = 30; // limitation of vertex
    bool isFilled = true;
    bool isActivated = false;
};

enum Obj_type
{
    NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
};

enum colorish
{
    WHITE, RED, GREEN, BLUE, COLORFUL
};

enum right_click
{
    CLEAR, EXIT
};

colorish color_type = WHITE;
Shape shapeList[MAX];
Color color_value;
Obj_type obj_type = NONE;

// start processing drawing
void StartDrawing(float x, float y)
{
    if (obj_type == NONE)
        return;
    else
    {
        cout << "Start drawing !\n";
        dragging = true; //start drawing
        shapeList[shapeCount].isActivated = true;
        switch (obj_type)
        {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
            case POLYGON:
                shapeList[shapeCount].type = "Polygon";
                break;
            case CIRCLE:
                shapeList[shapeCount].type = "Circle";
                break;
            case T_POINT:
                shapeList[shapeCount].type = "Point";
                shapeList[shapeCount].limit = 1;
                break;
            case LINE:
                shapeList[shapeCount].type = "Line";
                shapeList[shapeCount].limit = 2;
                break;
            case TRIANGLE:
                shapeList[shapeCount].type = "Triangle";
                shapeList[shapeCount].limit = 3;
                break;
            case TEXT:
                shapeList[shapeCount].type = "Text";
                break;
            /*case CIRCLE:
                shapeList[shapeCount].type = "";
                break;
            case CIRCLE:
                shapeList[shapeCount].type = "";
                break;*/
        }
        //cout << "your obj_type = " << obj_type << "\n";
        //cout << "Count = " << shapeCount << endl;
        //resize the vector of coordinate
        shapeList[shapeCount].pnt.reserve(shapeList[shapeCount].limit);
        if (color_type == COLORFUL)
        {
            Color tmp_color;
            srand(time(NULL));
            tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, tmp_color);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        else
        {
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, color_value);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        shapeList[shapeCount].pnt_size = point_size;
        shapeList[shapeCount].lineWidth = line_width;
        shapeList[shapeCount].limit--;
    }
}

// Continue drawing
void ContDrawing(float x, float y)
{
    if (!dragging)
        return;
    if (!shapeList[shapeCount].limit) // vertex is full
        return;

    cout << "limitation = " << shapeList[shapeCount].limit << endl;
    if (color_type == COLORFUL)
    {
        Color tmp_color;
        srand(time(NULL));
        tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, tmp_color);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    else
    {
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, color_value);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    shapeList[shapeCount].limit--;
    
    glutPostRedisplay();
}

// Finish Drawing
void FinishDrawing(float x, float y)
{
    if (!dragging)
        return;
    if (shapeList[shapeCount].limit == 0 || (shapeList[shapeCount].pnt.begin() != shapeList[shapeCount].pnt.end() - 1 &&
        ((*shapeList[shapeCount].pnt.begin()).first - (*shapeList[shapeCount].pnt.rbegin()).first == 0 && (*shapeList[shapeCount].pnt.begin()).second.first - (*shapeList[shapeCount].pnt.rbegin()).second.first == 0)))
    {
        dragging = false; // finish drawing
        shapeCount++;
        cout << "Finish\n";
    }
    glutPostRedisplay();
}

// Obj Menu
void objMenu(int value)
{
    switch (value)
    {
        case NONE:
            obj_type = NONE;
            break;
        case POLYGON:
            obj_type = POLYGON;
            break;
        case CIRCLE:
            obj_type = CIRCLE;
            break;
        case T_POINT:
            obj_type = T_POINT;
            break;
        case LINE:
            obj_type = LINE;
            break;
        case TRIANGLE:
            obj_type = TRIANGLE;
            break;
        case TEXT:
            obj_type = TEXT;
            break;
    }
}

// Color menu
void colorMenu(int value)
{
    switch(value)
    {
        case WHITE:
            color_value.r = 1.0f;
            color_value.g = 1.0f;
            color_value.b = 1.0f;
            break;
        case RED:
            color_value.r = 1.0f;
            color_value.g = 0.0f;
            color_value.b = 0.0f;
            break;
        case GREEN:
            color_value.r = 0.0f;
            color_value.g = 1.0f;
            color_value.b = 0.0f;
            break;
        case BLUE:
            color_value.r = 0.0f;
            color_value.g = 0.0f;
            color_value.b = 1.0f;
            break;
        case COLORFUL:
            color_type = COLORFUL;
            break;
        
    }
    glColor3f(color_value.r, color_value.g, color_value.b);
}

// Right-click menu
void rightClickMenu(int value)
{
    switch(value)
    {
        case CLEAR:
            // TODO:
            break;
        case EXIT:
            exit(0);
    }
}

// line width menu
void lineWidthMenu(int width)
{
    if (width == 1.0)
        line_width = 1.0;
    else if (width == 2.0)
        line_width = 2.0;
    else if (width == 3.0)
        line_width = 3.0;
    else if (width == 4.0)
        line_width = 4.0;
    else if (width == 5.0)
        line_width = 5.0;

    glutPostRedisplay();
}

// Point size menu
void pointSizeMenu(int size)
{
    if (size == 1.0)
        point_size = 1.0;
    else if (size == 2.0)
        point_size = 2.0;
    else if (size == 3.0)
        point_size = 3.0;
    else if (size == 4.0)
        point_size = 4.0;
    else if (size == 5.0)
        point_size = 5.0;
}

// Create right-click menu
void createMenu()
{
    int color_m, type_m, file_m;
    //create color sub-menu
    color_m = glutCreateMenu(colorMenu);
    glutAddMenuEntry("White", WHITE);
    glutAddMenuEntry("Red", RED);
    glutAddMenuEntry("Green", GREEN);
    glutAddMenuEntry("Blue", BLUE);
    glutAddMenuEntry("Colorful", COLORFUL);

    //create type sub-menu
    type_m = glutCreateMenu(objMenu);
    glutAddMenuEntry("None", NONE);
    glutAddMenuEntry("Polygon", POLYGON);
    glutAddMenuEntry("Circle", CIRCLE);
    glutAddMenuEntry("Point", T_POINT);
    glutAddMenuEntry("Line", LINE);
    glutAddMenuEntry("Triangle", TRIANGLE);
    glutAddMenuEntry("Text", TEXT);
    // type_m = glutCreateMenu()

    //create main menu
    glutCreateMenu(rightClickMenu);
    glutAddSubMenu("Colors", color_m);
    glutAddSubMenu("Object", type_m);
    glutAddMenuEntry("Clear", CLEAR);
    glutAddMenuEntry("Exit", EXIT);
    glutAttachMenu(GLUT_RIGHT_BUTTON);
}

// Process Normal Key
void processNormalKeys(unsigned char key, int x, int y)
{
    if (key == 'q' || key == 'Q')
        exit(0);
}

// draw main func
void Draw()
{
    for (int i = 0; i < shapeCount; i++)
    {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
        if (shapeList[i].type == "Polygon")
        {
            glBegin(GL_POLYGON);
            for (auto iter : shapeList[i].pnt)
            {
                glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
                glVertex2f(iter.first, iter.second.first);
            }
            glEnd();
        }
        if (shapeList[i].type == "Circle")
        {
            // TODO
        }
        if (shapeList[i].type == "Point")
        {
            glPointSize(shapeList[i].pnt_size);
            glColor3f(shapeList[i].pnt[0].second.second.r, shapeList[i].pnt[0].second.second.g, shapeList[i].pnt[0].second.second.b);
            glBegin(GL_POINTS);
            glVertex2f(shapeList[i].pnt[0].first, shapeList[i].pnt[0].second.first);
            glEnd();
        }
        if (shapeList[i].type == "Triangle")
        {
            glBegin(GL_TRIANGLES);
            for (auto iter : shapeList[i].pnt)
            {
                glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
                glVertex2f(iter.first, iter.second.first);
            }
            glEnd();
        }
        if (shapeList[i].type == "Line")
        {
            glLineWidth(shapeList[i].lineWidth);
            glColor3f(shapeList[i].pnt[0].second.second.r, shapeList[i].pnt[0].second.second.g, shapeList[i].pnt[0].second.second.b);
            glBegin(GL_LINES);
            glVertex2f(shapeList[i].pnt[0].first, shapeList[i].pnt[0].second.first);
            glVertex2f(shapeList[i].pnt[1].first, shapeList[i].pnt[1].second.first);
            glEnd();
        }
        if (shapeList[i].type == "Text")
        {
            //TODO
        }
    }
}

// Initialize
void init()
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glColor3f(1.0f, 1.0f, 1.0f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, (double) width, 0, (double) height);
}

void display_func(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0); //set window background color
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // clear color buffer
    glViewport(0, 0, width, height);
    Draw();
}

//reshape func
void changeSize(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    width = w; height = h;
    glOrtho(0.0, w, 0.0, h, -1.0, 1.0);
    glViewport(0, 0, w, h);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    glutPostRedisplay();
}

// Mouse Event
void mouse_func(int button, int state, int x, int y)
{
    if (button != GLUT_LEFT_BUTTON || state != GLUT_DOWN)
        return;

    float wx, wy;
    wx = (float)x;
    wy = (float)(height - y);

    if (shapeList[shapeCount].pnt.empty())
    {
        StartDrawing(wx, wy);
    }
    else
    {
        ContDrawing(wx, wy);
    }
    FinishDrawing(wx, wy);
}

// motion func
void motion(int x, int y)
{
    if (dragging)
    {
        float wx, wy;
        wx = (float)x;
        wy = (float)(height - y);

        //ContDrawing(wx, wy);
    }
}

int main(int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitWindowPosition(0, 0);
    glutInitWindowSize(width, height);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutCreateWindow("CGhw_2DPainter");
    createMenu();
    init();
    // TODO:
    
    glutDisplayFunc(display_func);// register callbacks
    
    glutReshapeFunc(changeSize);  
    glutKeyboardFunc(processNormalKeys);
    glutMouseFunc(mouse_func);
    glutMotionFunc(motion);
    
    glutMainLoop();
    return 1;
}
```

```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

// Window's width and height
int width = 800;
int height = 600;
const int MAX = 50; 
int shapeCount = 0;
float point_size = 1.0;
float line_width = 1.0;
float radius = 1.0;
bool dragging = false;
float PI = 3.1416f;
bool isFilled = true;

struct Color
{
    float r = 0.0f;
    float g = 0.0f;
    float b = 0.0f;
};

struct Shape
{
    string type;
    int id;
    vector< pair< float, pair< float, Color > > > pnt; // the coordinate and color of each shape
    float pnt_size = 1.0;
    float lineWidth = 1.0;
    float R = 1.0;
    int limit = 30; // limitation of vertex
    bool isFilled = true;
    bool isActivated = false;
    string str = "";
};

enum Obj_type
{
    NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT, CONTINUOUS_LINE
};

enum colorish
{
    WHITE, RED, GREEN, BLUE, COLORFUL
};

enum right_click
{
    CLEAR, REDO, UNDO, EXIT
};

colorish color_type = WHITE;
Shape shapeList[MAX];
Color color_value;
Obj_type obj_type = NONE;
stack< Shape > redo, undo;

// start processing drawing
void StartDrawing(float x, float y)
{
    if (obj_type == NONE)
        return;
    else
    {
        cout << "Start drawing !\n";
        dragging = true; //start drawing
        string tmp;
        shapeList[shapeCount].isActivated = true;
        switch (obj_type)
        {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
            case POLYGON:
                shapeList[shapeCount].type = "Polygon";
                break;
            case CIRCLE:
                shapeList[shapeCount].type = "Circle";
                shapeList[shapeCount].R = radius;
                shapeList[shapeCount].limit = 1;
                break;
            case T_POINT:
                shapeList[shapeCount].type = "Point";
                shapeList[shapeCount].limit = 1;
                break;
            case LINE:
                shapeList[shapeCount].type = "Line";
                shapeList[shapeCount].limit = 2;
                break;
            case TRIANGLE:
                shapeList[shapeCount].type = "Triangle";
                shapeList[shapeCount].limit = 3;
                break;
            case TEXT:
                shapeList[shapeCount].type = "Text";
                shapeList[shapeCount].limit = 1;
                cout << "Type the text here: ";
                cin >> tmp;
                shapeList[shapeCount].str = tmp;
                break;
            case CONTINUOUS_LINE:
                shapeList[shapeCount].type = "ContinuousLine";
                
                break;
            /*case CIRCLE:
                shapeList[shapeCount].type = "";
                break;*/
        }
        //cout << "your obj_type = " << obj_type << "\n";
        //cout << "Count = " << shapeCount << endl;
        //resize the vector of coordinate
        shapeList[shapeCount].pnt.reserve(shapeList[shapeCount].limit);
        if (color_type == COLORFUL)
        {
            Color tmp_color;
            srand(time(NULL));
            tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, tmp_color);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        else
        {
            
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, color_value);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        shapeList[shapeCount].isFilled = isFilled;
        shapeList[shapeCount].pnt_size = point_size;
        shapeList[shapeCount].lineWidth = line_width;
        shapeList[shapeCount].limit--;
        shapeList[shapeCount].isActivated = true;
        shapeList[shapeCount].id = shapeCount;
        undo.push(shapeList[shapeCount]);
    }
}

// Continue drawing
void ContDrawing(float x, float y)
{
    if (!dragging)
        return;
    if (!shapeList[shapeCount].limit) // vertex is full
        return;

    cout << "limitation = " << shapeList[shapeCount].limit << endl;
    if (color_type == COLORFUL)
    {
        Color tmp_color;
        srand(time(NULL));
        tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, tmp_color);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    else
    {
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, color_value);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    shapeList[shapeCount].limit--;
    
    glutPostRedisplay();
}

// Finish Drawing
void FinishDrawing(float x, float y)
{
    if (!dragging)
        return;
    if (shapeList[shapeCount].limit == 0 || (shapeList[shapeCount].pnt.begin() != shapeList[shapeCount].pnt.end() - 1 &&
        ((*shapeList[shapeCount].pnt.rbegin()).first - (*shapeList[shapeCount].pnt.rbegin()).first == 0 && (*shapeList[shapeCount].pnt.begin()).second.first - (*shapeList[shapeCount].pnt.rbegin()).second.first == 0)))
    {
        dragging = false; // finish drawing
        shapeCount++;
        cout << "Finish\n";
    }
    glutPostRedisplay();
}

// Obj Menu
void objMenu(int value)
{
    switch (value)
    {
        case NONE:
            obj_type = NONE;
            break;
        case POLYGON:
            obj_type = POLYGON;
            break;
        case CIRCLE:
            obj_type = CIRCLE;
            break;
        case T_POINT:
            obj_type = T_POINT;
            break;
        case LINE:
            obj_type = LINE;
            break;
        case TRIANGLE:
            obj_type = TRIANGLE;
            break;
        case TEXT:
            obj_type = TEXT;
            break;
        case CONTINUOUS_LINE:
            obj_type = CONTINUOUS_LINE;
            break;
    }
}

// Color menu
void colorMenu(int value)
{
    switch(value)
    {
        case WHITE:
            color_type = WHITE;
            color_value.r = 1.0f;
            color_value.g = 1.0f;
            color_value.b = 1.0f;
            break;
        case RED:
            color_type = RED;
            color_value.r = 1.0f;
            color_value.g = 0.0f;
            color_value.b = 0.0f;
            break;
        case GREEN:
            color_type = GREEN;
            color_value.r = 0.0f;
            color_value.g = 1.0f;
            color_value.b = 0.0f;
            break;
        case BLUE:
            color_type = BLUE;
            color_value.r = 0.0f;
            color_value.g = 0.0f;
            color_value.b = 1.0f;
            break;
        case COLORFUL:
            color_type = COLORFUL;
            break;
        
    }
    glColor3f(color_value.r, color_value.g, color_value.b);
}

//Clear Attribute
void ClearAttri()
{
    for (int i = 0; i < shapeCount; i++)
    {
        //shapeList[i].pnt.clear();
        shapeList[i].isActivated = false;
    }
    glutPostRedisplay();
    return;
}

void Redo()
{
    if (redo.empty())
    {
        cout << "There is nothing can redo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = redo.top();
        redo.pop();
        shapeList[tmp.id].isActivated = true;
        undo.push(shapeList[tmp.id]);
        glutPostRedisplay();
        return;
    }
}

//Undo
void Undo()
{
    if (undo.empty())
    {
        cout << "There is nothing can undo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = undo.top();
        undo.pop();
        shapeList[tmp.id].isActivated = false;
        redo.push(shapeList[tmp.id]);
        glutPostRedisplay();
        return;
    }
}

// Right-click menu
void rightClickMenu(int value)
{
    switch(value)
    {
        case CLEAR:
            ClearAttri();
            break;
        case REDO:
            Redo();
            break;
        case UNDO:
            Undo();
            break;
        case EXIT:
            exit(0);
    }
}

//Filled or not
void filledMenu(int value)
{
    if (value == 1)
        isFilled = true;
    else if (value == 2)
        isFilled = false;
}

//radius menu
void radiusMenu(int r)
{
    if (r == 1.0)
        radius = 1.0;
    else if (r == 2.0)
        radius = 2.0;
    else if (r == 3.0)
        radius = 3.0;
    else if (r == 4.0)
        radius = 4.0;
    else if (r == 5.0)
        radius = 5.0;
}

// line width menu
void lineWidthMenu(int width)
{
    if (width == 1.0)
        line_width = 1.0;
    else if (width == 2.0)
        line_width = 2.0;
    else if (width == 3.0)
        line_width = 3.0;
    else if (width == 4.0)
        line_width = 4.0;
    else if (width == 5.0)
        line_width = 5.0;

    glutPostRedisplay();
}

// Point size menu
void pointSizeMenu(int size)
{
    if (size == 1.0)
        point_size = 1.0;
    else if (size == 2.0)
        point_size = 2.0;
    else if (size == 3.0)
        point_size = 3.0;
    else if (size == 4.0)
        point_size = 4.0;
    else if (size == 5.0)
        point_size = 5.0;
}

// Create right-click menu
void createMenu()
{
    int color_m, type_m, file_m, type_p, type_l, type_r, type_f;
    //create color sub-menu
    color_m = glutCreateMenu(colorMenu);
    glutAddMenuEntry("White", WHITE);
    glutAddMenuEntry("Red", RED);
    glutAddMenuEntry("Green", GREEN);
    glutAddMenuEntry("Blue", BLUE);
    glutAddMenuEntry("Colorful", COLORFUL);

    //create type sub-menu
    type_m = glutCreateMenu(objMenu);
    glutAddMenuEntry("None", NONE);
    glutAddMenuEntry("Polygon", POLYGON);
    glutAddMenuEntry("Circle", CIRCLE);
    glutAddMenuEntry("Point", T_POINT);
    glutAddMenuEntry("Line", LINE);
    glutAddMenuEntry("Triangle", TRIANGLE);
    glutAddMenuEntry("Text", TEXT);

    //create point size sub-menu
    type_p = glutCreateMenu(pointSizeMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create line width menu
    type_l = glutCreateMenu(lineWidthMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create radius menu
    type_r = glutCreateMenu(radiusMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create filled menu
    type_f = glutCreateMenu(filledMenu);
    glutAddMenuEntry("Filled", 1);
    glutAddMenuEntry("NotFilled", 2);

    //create main menu
    glutCreateMenu(rightClickMenu);
    glutAddSubMenu("Colors", color_m);
    glutAddSubMenu("Object", type_m);
    glutAddSubMenu("PointSize", type_p);
    glutAddSubMenu("LineWidth", type_l);
    glutAddSubMenu("Radius", type_r);
    glutAddSubMenu("Filled", type_f);
    glutAddMenuEntry("Redo", REDO);
    glutAddMenuEntry("Undo", UNDO);
    glutAddMenuEntry("Clear", CLEAR);
    glutAddMenuEntry("Exit", EXIT);
    glutAttachMenu(GLUT_RIGHT_BUTTON);
}

// Process Normal Key
void processNormalKeys(unsigned char key, int x, int y)
{
    if (key == 'q' || key == 'Q')
        exit(0);
}

//draw circle
void DrawCircle(struct Shape Cir)
{
    float x, y;
    x = Cir.pnt[0].first;
    y = Cir.pnt[0].second.first;
    float twicePI = 2.0 * PI;
    if (Cir.isFilled)
    {
        int triangleAmount = 50;//要用多少三角形畫出圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glBegin(GL_TRIANGLE_FAN);
        glVertex2f(x, y);
        for (int i = 0; i < triangleAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / triangleAmount)),
                y + (Cir.R * sin(i * twicePI / triangleAmount)));
        }
        glEnd();
    }
    else
    {
        int lineAmount = 150;//要用多少線來畫圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glBegin(GL_LINE_LOOP);
        for (int i = 0; i < lineAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / lineAmount)),
                y + (Cir.R * sin(i * twicePI / lineAmount)));
        }
        glEnd();
    }
    glutPostRedisplay();
}

//draw string
void DrawString(struct Shape Str)
{
    glColor3f(Str.pnt[0].second.second.r, Str.pnt[0].second.second.g, Str.pnt[0].second.second.b);
    glRasterPos2f(Str.pnt[0].first, Str.pnt[0].second.first);
    for (int i = 0; i < Str.str.size(); i++)
    {
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, Str.str[i]);
    }
    glutPostRedisplay();
}

//draw polygon
void DrawPolygon(struct Shape pol)
{
    if (pol.isFilled)
    {
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Point
void DrawPoint(struct Shape P)
{
    glPointSize(P.pnt_size);
    glColor3f(P.pnt[0].second.second.r, P.pnt[0].second.second.g, P.pnt[0].second.second.b);
    glBegin(GL_POINTS);
    glVertex2f(P.pnt[0].first, P.pnt[0].second.first);
    glEnd();
    glutPostRedisplay();
}

//Draw Triangle
void DrawTriangle(struct Shape Tri)
{
    if (Tri.isFilled)
    {
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Line
void DrawLine(struct Shape L)
{
    glLineWidth(L.lineWidth);
    glColor3f(L.pnt[0].second.second.r, L.pnt[0].second.second.g, L.pnt[0].second.second.b);
    glBegin(GL_LINES);
    glVertex2f(L.pnt[0].first, L.pnt[0].second.first);
    glVertex2f(L.pnt[1].first, L.pnt[1].second.first);
    glEnd();
    glutPostRedisplay();
}

// draw main func
void Draw()
{
    for (int i = 0; i < shapeCount; i++)
    {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
        if (shapeList[i].isActivated)
        {
            if (shapeList[i].type == "Polygon")
            {
                DrawPolygon(shapeList[i]);
            }
            if (shapeList[i].type == "Circle")
            {
                DrawCircle(shapeList[i]);
            }
            if (shapeList[i].type == "Point")
            {
                DrawPoint(shapeList[i]);
            }
            if (shapeList[i].type == "Triangle")
            {
                DrawTriangle(shapeList[i]);
            }
            if (shapeList[i].type == "Line")
            {
                DrawLine(shapeList[i]);
            }
            if (shapeList[i].type == "Text")
            {
                DrawString(shapeList[i]);
            }
        }
    }
    //glutPostRedisplay();
}

// Initialize
void init()
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glColor3f(1.0f, 1.0f, 1.0f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, (double) width, 0, (double) height);
}

void display_func(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0); //set window background color
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // clear color buffer
    glViewport(0, 0, width, height);
    Draw();
}

//reshape func
void changeSize(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    width = w; height = h;
    glOrtho(0.0, w, 0.0, h, -1.0, 1.0);
    glViewport(0, 0, w, h);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    glutPostRedisplay();
}

// Mouse Event
void mouse_func(int button, int state, int x, int y)
{
    if (button != GLUT_LEFT_BUTTON || state != GLUT_DOWN)
        return;

    float wx, wy;
    wx = (float)x;
    wy = (float)(height - y);

    if (shapeList[shapeCount].pnt.empty())
    {
        StartDrawing(wx, wy);
    }
    else
    {
        ContDrawing(wx, wy);
    }
    FinishDrawing(wx, wy);
}

// motion func
void motion(int x, int y)
{
    if (dragging)
    {
        float wx, wy;
        wx = (float)x;
        wy = (float)(height - y);
        //TODO
        //ContDrawing(wx, wy);
    }
}

int main(int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitWindowPosition(0, 0);
    glutInitWindowSize(width, height);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutCreateWindow("CGhw_2DPainter");
    createMenu();
    init();
    // TODO:
    
    glutDisplayFunc(display_func);// register callbacks
    
    glutReshapeFunc(changeSize);  
    glutKeyboardFunc(processNormalKeys);
    glutMouseFunc(mouse_func);
    glutMotionFunc(motion);
    
    glutMainLoop();
    return 1;
}
```
```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

// Window's width and height
int width = 800;
int height = 600;
const int MAX = 50; 
int shapeCount = 0;
float point_size = 1.0;
float line_width = 1.0;
float radius = 1.0;
bool dragging = false;
bool drawing = false;
float PI = 3.1416f;
bool isFilled = true;

struct Color
{
    float r = 0.0f;
    float g = 0.0f;
    float b = 0.0f;
};

struct Shape
{
    string type;
    int id;
    vector< pair< float, pair< float, Color > > > pnt; // the coordinate and color of each shape
    float pnt_size = 1.0;
    float lineWidth = 1.0;
    float R = 1.0;
    int limit = 30; // limitation of vertex
    bool isFilled = true;
    bool isActivated = false;
    string str = "";
};

enum Obj_type
{
    NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT, CONTINUOUS_LINE
};

enum colorish
{
    WHITE, RED, GREEN, BLUE, COLORFUL
};

enum right_click
{
    CLEAR, REDO, UNDO, EXIT
};

colorish color_type = WHITE;
Shape shapeList[MAX];
Color color_value;
Obj_type obj_type = NONE;
stack< Shape > redo, undo;

// start processing drawing
void StartDrawing(float x, float y)
{
    if (obj_type == NONE)
        return;
    else
    {
        cout << "Start drawing !\n";
        drawing = true; //start drawing
        string tmp;
        shapeList[shapeCount].isActivated = true;
        switch (obj_type)
        {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
            case POLYGON:
                shapeList[shapeCount].type = "Polygon";
                break;
            case CIRCLE:
                shapeList[shapeCount].type = "Circle";
                shapeList[shapeCount].R = radius;
                shapeList[shapeCount].limit = 1;
                break;
            case T_POINT:
                shapeList[shapeCount].type = "Point";
                shapeList[shapeCount].limit = 1;
                break;
            case LINE:
                shapeList[shapeCount].type = "Line";
                shapeList[shapeCount].limit = 2;
                break;
            case TRIANGLE:
                shapeList[shapeCount].type = "Triangle";
                shapeList[shapeCount].limit = 3;
                break;
            case TEXT:
                shapeList[shapeCount].type = "Text";
                shapeList[shapeCount].limit = 1;
                cout << "Type the text here: ";
                cin >> tmp;
                shapeList[shapeCount].str = tmp;
                break;
            case CONTINUOUS_LINE:
                shapeList[shapeCount].type = "ContinuousLine";
                
                break;
            /*case CIRCLE:
                shapeList[shapeCount].type = "";
                break;*/
        }
        //cout << "your obj_type = " << obj_type << "\n";
        //cout << "Count = " << shapeCount << endl;
        //resize the vector of coordinate
        shapeList[shapeCount].pnt.reserve(shapeList[shapeCount].limit);
        if (color_type == COLORFUL)
        {
            Color tmp_color;
            srand(time(NULL));
            tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
            tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, tmp_color);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        else
        {
            
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, color_value);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        shapeList[shapeCount].isFilled = isFilled;
        shapeList[shapeCount].pnt_size = point_size;
        shapeList[shapeCount].lineWidth = line_width;
        shapeList[shapeCount].limit--;
        shapeList[shapeCount].isActivated = true;
        shapeList[shapeCount].id = shapeCount;
        undo.push(shapeList[shapeCount]);
    }
}

// Continue drawing
void ContDrawing(float x, float y)
{
    if (!drawing)
        return;
    if (!shapeList[shapeCount].limit) // vertex is full
        return;

    cout << "limitation = " << shapeList[shapeCount].limit << endl;
    if (color_type == COLORFUL)
    {
        Color tmp_color;
        srand(time(NULL));
        tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, tmp_color);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    else
    {
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, color_value);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    shapeList[shapeCount].limit--;
    
    glutPostRedisplay();
}

// Finish Drawing
void FinishDrawing(float x, float y)
{
    if (!drawing)
        return;
    if (shapeList[shapeCount].limit == 0 || (shapeList[shapeCount].pnt.begin() != shapeList[shapeCount].pnt.end() - 1 &&
        ((*shapeList[shapeCount].pnt.rbegin()).first - (*shapeList[shapeCount].pnt.rbegin()).first == 0 && (*shapeList[shapeCount].pnt.begin()).second.first - (*shapeList[shapeCount].pnt.rbegin()).second.first == 0)))
    {
        dragging = false; // finish drawing
        shapeCount++;
        cout << "Finish\n";
    }
    glutPostRedisplay();
}

// Obj Menu
void objMenu(int value)
{
    switch (value)
    {
        case NONE:
            obj_type = NONE;
            break;
        case POLYGON:
            obj_type = POLYGON;
            break;
        case CIRCLE:
            obj_type = CIRCLE;
            break;
        case T_POINT:
            obj_type = T_POINT;
            break;
        case LINE:
            obj_type = LINE;
            break;
        case TRIANGLE:
            obj_type = TRIANGLE;
            break;
        case TEXT:
            obj_type = TEXT;
            break;
        case CONTINUOUS_LINE:
            obj_type = CONTINUOUS_LINE;
            break;
    }
}

// Color menu
void colorMenu(int value)
{
    switch(value)
    {
        case WHITE:
            color_type = WHITE;
            color_value.r = 1.0f;
            color_value.g = 1.0f;
            color_value.b = 1.0f;
            break;
        case RED:
            color_type = RED;
            color_value.r = 1.0f;
            color_value.g = 0.0f;
            color_value.b = 0.0f;
            break;
        case GREEN:
            color_type = GREEN;
            color_value.r = 0.0f;
            color_value.g = 1.0f;
            color_value.b = 0.0f;
            break;
        case BLUE:
            color_type = BLUE;
            color_value.r = 0.0f;
            color_value.g = 0.0f;
            color_value.b = 1.0f;
            break;
        case COLORFUL:
            color_type = COLORFUL;
            break;
        
    }
    glColor3f(color_value.r, color_value.g, color_value.b);
}

//Clear Attribute
void ClearAttri()
{
    for (int i = 0; i < shapeCount; i++)
    {
        //shapeList[i].pnt.clear();
        shapeList[i].isActivated = false;
    }
    glutPostRedisplay();
    return;
}

void Redo()
{
    if (redo.empty())
    {
        cout << "There is nothing can redo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = redo.top();
        redo.pop();
        shapeList[tmp.id].isActivated = true;
        undo.push(shapeList[tmp.id]);
        glutPostRedisplay();
        return;
    }
}

//Undo
void Undo()
{
    if (undo.empty())
    {
        cout << "There is nothing can undo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = undo.top();
        undo.pop();
        shapeList[tmp.id].isActivated = false;
        redo.push(shapeList[tmp.id]);
        glutPostRedisplay();
        return;
    }
}

// Right-click menu
void rightClickMenu(int value)
{
    switch(value)
    {
        case CLEAR:
            ClearAttri();
            break;
        case REDO:
            Redo();
            break;
        case UNDO:
            Undo();
            break;
        case EXIT:
            exit(0);
    }
}

//Filled or not
void filledMenu(int value)
{
    if (value == 1)
        isFilled = true;
    else if (value == 2)
        isFilled = false;
}

//radius menu
void radiusMenu(int r)
{
    if (r == 1.0)
        radius = 1.0;
    else if (r == 2.0)
        radius = 2.0;
    else if (r == 3.0)
        radius = 3.0;
    else if (r == 4.0)
        radius = 4.0;
    else if (r == 5.0)
        radius = 5.0;
}

// line width menu
void lineWidthMenu(int width)
{
    if (width == 1.0)
        line_width = 1.0;
    else if (width == 2.0)
        line_width = 2.0;
    else if (width == 3.0)
        line_width = 3.0;
    else if (width == 4.0)
        line_width = 4.0;
    else if (width == 5.0)
        line_width = 5.0;

    glutPostRedisplay();
}

// Point size menu
void pointSizeMenu(int size)
{
    if (size == 1.0)
        point_size = 1.0;
    else if (size == 2.0)
        point_size = 2.0;
    else if (size == 3.0)
        point_size = 3.0;
    else if (size == 4.0)
        point_size = 4.0;
    else if (size == 5.0)
        point_size = 5.0;
}

// Create right-click menu
void createMenu()
{
    int color_m, type_m, file_m, type_p, type_l, type_r, type_f;
    //create color sub-menu
    color_m = glutCreateMenu(colorMenu);
    glutAddMenuEntry("White", WHITE);
    glutAddMenuEntry("Red", RED);
    glutAddMenuEntry("Green", GREEN);
    glutAddMenuEntry("Blue", BLUE);
    glutAddMenuEntry("Colorful", COLORFUL);

    //create type sub-menu
    type_m = glutCreateMenu(objMenu);
    glutAddMenuEntry("None", NONE);
    glutAddMenuEntry("Polygon", POLYGON);
    glutAddMenuEntry("Circle", CIRCLE);
    glutAddMenuEntry("Point", T_POINT);
    glutAddMenuEntry("Line", LINE);
    glutAddMenuEntry("Triangle", TRIANGLE);
    glutAddMenuEntry("Text", TEXT);

    //create point size sub-menu
    type_p = glutCreateMenu(pointSizeMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create line width menu
    type_l = glutCreateMenu(lineWidthMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create radius menu
    type_r = glutCreateMenu(radiusMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create filled menu
    type_f = glutCreateMenu(filledMenu);
    glutAddMenuEntry("Filled", 1);
    glutAddMenuEntry("NotFilled", 2);

    //create main menu
    glutCreateMenu(rightClickMenu);
    glutAddSubMenu("Colors", color_m);
    glutAddSubMenu("Object", type_m);
    glutAddSubMenu("PointSize", type_p);
    glutAddSubMenu("LineWidth", type_l);
    glutAddSubMenu("Radius", type_r);
    glutAddSubMenu("Filled", type_f);
    glutAddMenuEntry("Redo", REDO);
    glutAddMenuEntry("Undo", UNDO);
    glutAddMenuEntry("Clear", CLEAR);
    glutAddMenuEntry("Exit", EXIT);
    glutAttachMenu(GLUT_RIGHT_BUTTON);
}

// Process Normal Key
void processNormalKeys(unsigned char key, int x, int y)
{
    if (key == 'q' || key == 'Q')
        exit(0);
}

//draw circle
void DrawCircle(struct Shape Cir)
{
    float x, y;
    x = Cir.pnt[0].first;
    y = Cir.pnt[0].second.first;
    float twicePI = 2.0 * PI;
    if (Cir.isFilled)
    {
        int triangleAmount = 50;//要用多少三角形畫出圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glBegin(GL_TRIANGLE_FAN);
        glVertex2f(x, y);
        for (int i = 0; i < triangleAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / triangleAmount)),
                y + (Cir.R * sin(i * twicePI / triangleAmount)));
        }
        glEnd();
    }
    else
    {
        int lineAmount = 150;//要用多少線來畫圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glBegin(GL_LINE_LOOP);
        for (int i = 0; i < lineAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / lineAmount)),
                y + (Cir.R * sin(i * twicePI / lineAmount)));
        }
        glEnd();
    }
    glutPostRedisplay();
}

//draw string
void DrawString(struct Shape Str)
{
    glColor3f(Str.pnt[0].second.second.r, Str.pnt[0].second.second.g, Str.pnt[0].second.second.b);
    glRasterPos2f(Str.pnt[0].first, Str.pnt[0].second.first);
    for (int i = 0; i < Str.str.size(); i++)
    {
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, Str.str[i]);
    }
    glutPostRedisplay();
}

//draw polygon
void DrawPolygon(struct Shape pol)
{
    if (pol.isFilled)
    {
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Point
void DrawPoint(struct Shape P)
{
    glPointSize(P.pnt_size);
    glColor3f(P.pnt[0].second.second.r, P.pnt[0].second.second.g, P.pnt[0].second.second.b);
    glBegin(GL_POINTS);
    glVertex2f(P.pnt[0].first, P.pnt[0].second.first);
    glEnd();
    glutPostRedisplay();
}

//Draw Triangle
void DrawTriangle(struct Shape Tri)
{
    if (Tri.isFilled)
    {
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Line
void DrawLine(struct Shape L)
{
    glLineWidth(L.lineWidth);
    glColor3f(L.pnt[0].second.second.r, L.pnt[0].second.second.g, L.pnt[0].second.second.b);
    glBegin(GL_LINES);
    glVertex2f(L.pnt[0].first, L.pnt[0].second.first);
    glVertex2f(L.pnt[1].first, L.pnt[1].second.first);
    glEnd();
    glutPostRedisplay();
}

// draw main func
void Draw()
{
    for (int i = 0; i < shapeCount; i++)
    {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
        if (shapeList[i].isActivated)
        {
            if (shapeList[i].type == "Polygon")
            {
                DrawPolygon(shapeList[i]);
            }
            if (shapeList[i].type == "Circle")
            {
                DrawCircle(shapeList[i]);
            }
            if (shapeList[i].type == "Point")
            {
                DrawPoint(shapeList[i]);
            }
            if (shapeList[i].type == "Triangle")
            {
                DrawTriangle(shapeList[i]);
            }
            if (shapeList[i].type == "Line")
            {
                DrawLine(shapeList[i]);
            }
            if (shapeList[i].type == "Text")
            {
                DrawString(shapeList[i]);
            }
        }
    }
    //glutPostRedisplay();
}

// Initialize
void init()
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glColor3f(1.0f, 1.0f, 1.0f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, (double) width, 0, (double) height);
}

void display_func(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0); //set window background color
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // clear color buffer
    glViewport(0, 0, width, height);
    Draw();
}

//reshape func
void changeSize(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    width = w; height = h;
    glOrtho(0.0, w, 0.0, h, -1.0, 1.0);
    glViewport(0, 0, w, h);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    glutPostRedisplay();
}

// Mouse Event
void mouse_func(int button, int state, int x, int y)
{
    if (button != GLUT_LEFT_BUTTON || state != GLUT_DOWN)
        return;

    float wx, wy;
    wx = (float)x;
    wy = (float)(height - y);

    if (shapeList[shapeCount].pnt.empty())
    {
        StartDrawing(wx, wy);
    }
    else
    {
        ContDrawing(wx, wy);
    }
    FinishDrawing(wx, wy);
}

// motion func
void motion(int x, int y)
{
    if (dragging)
    {
        float wx, wy;
        wx = (float)x;
        wy = (float)(height - y);
        //TODO
        //ContDrawing(wx, wy);
    }
}

int main(int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitWindowPosition(0, 0);
    glutInitWindowSize(width, height);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutCreateWindow("CGhw_2DPainter");
    createMenu();
    init();
    // TODO:
    
    glutDisplayFunc(display_func);// register callbacks
    
    glutReshapeFunc(changeSize);  
    glutKeyboardFunc(processNormalKeys);
    glutMouseFunc(mouse_func);
    glutMotionFunc(motion);
    
    glutMainLoop();
    return 1;
}
```
```cpp=
#include <bits/stdc++.h>
#include <GL/glut.h>

using namespace std;

// Window's width and height
int width = 800;
int height = 600;
const int MAX = 50; 
int shapeCount = 0;
int clearCount = 0;
float point_size = 1.0;
float line_width = 1.0;
float radius = 1.0;
bool dragging = false;
bool drawing = false;
float PI = 3.1416f;
bool isFilled = true;

struct Color
{
    float r = 0.0f;
    float g = 0.0f;
    float b = 0.0f;
};

struct Shape
{
    string type;
    int id;
    vector< pair< float, pair< float, Color > > > pnt; // the coordinate and color of each shape
    float pnt_size = 1.0;
    float lineWidth = 1.0;
    float R = 1.0;
    int limit = 30; // limitation of vertex
    bool isFilled = true;
    bool isActivated = false;
    string str = "";
    string status = "";  // status for clear
    int clr_id; // no. of clearArr
};

enum Obj_type
{
    NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT, CONTINUOUS_LINE, ERASER
};

enum colorish
{
    WHITE, RED, GREEN, BLUE, COLORFUL
};

enum right_click
{
    CLEAR, REDO, UNDO, EXIT
};

colorish color_type = WHITE;
Shape shapeList[MAX];
Color color_value;
Obj_type obj_type = NONE;
stack< Shape > redo, undo;
vector< int > clearID;
vector< vector< int > > vecID;

// start processing drawing
void StartDrawing(float x, float y)
{
    if (obj_type == NONE)
        return;
    else
    {
        cout << "Start drawing !\n";
        drawing = true; //start drawing
        string tmp;
        shapeList[shapeCount].isActivated = true;
        switch (obj_type)
        {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
            case POLYGON:
                shapeList[shapeCount].type = "Polygon";
                break;
            case CIRCLE:
                shapeList[shapeCount].type = "Circle";
                shapeList[shapeCount].R = radius;
                shapeList[shapeCount].limit = 1;
                break;
            case T_POINT:
                shapeList[shapeCount].type = "Point";
                shapeList[shapeCount].limit = 1;
                break;
            case LINE:
                shapeList[shapeCount].type = "Line";
                shapeList[shapeCount].limit = 2;
                break;
            case TRIANGLE:
                shapeList[shapeCount].type = "Triangle";
                shapeList[shapeCount].limit = 3;
                break;
            case TEXT:
                shapeList[shapeCount].type = "Text";
                shapeList[shapeCount].limit = 1;
                cout << "Type the text here: ";
                cin >> tmp;
                shapeList[shapeCount].str = tmp;
                break;
            case CONTINUOUS_LINE:
                shapeList[shapeCount].type = "ContinuousLine";
                shapeList[shapeCount].limit = 5000;
                dragging = true;
                break;
            case ERASER:
                shapeList[shapeCount].type = "Eraser";
                shapeList[shapeCount].limit = 50000;
                dragging = true;
                break;
        }
        //cout << "your obj_type = " << obj_type << "\n";
        //cout << "Count = " << shapeCount << endl;
        //resize the vector of coordinate
        shapeList[shapeCount].pnt.reserve(shapeList[shapeCount].limit);
        if (shapeList[shapeCount].type == "Eraser")
        {
            Color tmp_col;
            tmp_col.r = tmp_col.g = tmp_col.b = 0.0;
            pair< float, Color > tmp_sec;
            tmp_sec = make_pair(y, tmp_col);
            shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
        }
        else
        {
            if (color_type == COLORFUL)
            {
                Color tmp_color;
                srand(time(NULL));
                tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
                tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
                tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
                pair< float, Color > tmp_sec;
                tmp_sec = make_pair(y, tmp_color);
                shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
            }
            else
            {
                pair< float, Color > tmp_sec;
                tmp_sec = make_pair(y, color_value);
                shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
            }
        }
        shapeList[shapeCount].isFilled = isFilled;
        shapeList[shapeCount].pnt_size = point_size;
        shapeList[shapeCount].lineWidth = line_width;
        shapeList[shapeCount].limit--;
        shapeList[shapeCount].isActivated = true;
        shapeList[shapeCount].id = shapeCount;
        undo.push(shapeList[shapeCount]);
    }
}

// Continue drawing
void ContDrawing(float x, float y)
{
    if (!drawing)
        return;
    if (!shapeList[shapeCount].limit) // vertex is full
        return;
    if(obj_type == LINE || obj_type == TRIANGLE)
        cout << "The number of the rest = " << shapeList[shapeCount].limit << endl;
    if (obj_type == POLYGON)
        cout << "Double click to quit drawing\n";
    if (color_type == COLORFUL)
    {
        Color tmp_color;
        srand(time(NULL));
        tmp_color.r = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.g = (float)rand() / (RAND_MAX + 1.0);
        tmp_color.b = (float)rand() / (RAND_MAX + 1.0);
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, tmp_color);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    else if (obj_type == ERASER)
    {
        Color tmp_col;
        tmp_col.r = tmp_col.g = tmp_col.b = 0.0;
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, tmp_col);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    else
    {
        pair< float, Color > tmp_sec;
        tmp_sec = make_pair(y, color_value);
        shapeList[shapeCount].pnt.push_back(make_pair(x, tmp_sec));
    }
    shapeList[shapeCount].limit--;
    
    glutPostRedisplay();
}

// Finish Drawing
void FinishDrawing(float x, float y)
{
    if (!drawing)
        return;
    if (shapeList[shapeCount].limit == 0)
    {/*|| (shapeList[shapeCount].pnt.begin() != shapeList[shapeCount].pnt.end() - 1 &&
        ((*shapeList[shapeCount].pnt.rbegin()).first - (*shapeList[shapeCount].pnt.rbegin()).first == 0 && (*shapeList[shapeCount].pnt.begin()).second.first - (*shapeList[shapeCount].pnt.rbegin()).second.first == 0))*/
        drawing = false; // finish drawing
        shapeCount++;
        cout << "Finish\n";
    }
    glutPostRedisplay();
}

// Obj Menu
void objMenu(int value)
{
    switch (value)
    {
        case NONE:
            obj_type = NONE;
            break;
        case POLYGON:
            obj_type = POLYGON;
            break;
        case CIRCLE:
            obj_type = CIRCLE;
            break;
        case T_POINT:
            obj_type = T_POINT;
            break;
        case LINE:
            obj_type = LINE;
            break;
        case TRIANGLE:
            obj_type = TRIANGLE;
            break;
        case TEXT:
            obj_type = TEXT;
            break;
        case CONTINUOUS_LINE:
            obj_type = CONTINUOUS_LINE;
            break;
        case ERASER:
            obj_type = ERASER;
    }
}

// Color menu
void colorMenu(int value)
{
    switch(value)
    {
        case WHITE:
            color_type = WHITE;
            color_value.r = 1.0f;
            color_value.g = 1.0f;
            color_value.b = 1.0f;
            break;
        case RED:
            color_type = RED;
            color_value.r = 1.0f;
            color_value.g = 0.0f;
            color_value.b = 0.0f;
            break;
        case GREEN:
            color_type = GREEN;
            color_value.r = 0.0f;
            color_value.g = 1.0f;
            color_value.b = 0.0f;
            break;
        case BLUE:
            color_type = BLUE;
            color_value.r = 0.0f;
            color_value.g = 0.0f;
            color_value.b = 1.0f;
            break;
        case COLORFUL:
            color_type = COLORFUL;
            break;
        
    }
    glColor3f(color_value.r, color_value.g, color_value.b);
}

//Clear Attribute
void ClearAttri()
{
    if (!clearID.empty())
        clearID.clear();
    for (int i = 0; i < shapeCount; i++)
    {
        if (shapeList[i].isActivated)
        {
            clearID.push_back(shapeList[i].id);
            shapeList[i].isActivated = false;
        }
    }
    vecID.push_back(clearID);
    clearID.clear();
    Shape tmp;
    tmp.status = "Clear";
    tmp.clr_id = clearCount;
    undo.push(tmp);
    clearCount++;
    glutPostRedisplay();
    return;
}

//undo clear
void UndoClear(int id)
{
    for (int i = 0; i < vecID[id].size(); i++)
    {
        shapeList[vecID[id][i]].isActivated = true;
    }
}

//redo clear
void RedoClear(int id)
{
    for (int i = 0; i < vecID[id].size(); i++)
    {
        shapeList[vecID[id][i]].isActivated = false;
    }
}

void Redo()
{
    if (redo.empty())
    {
        cout << "There is nothing can redo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = redo.top();
        redo.pop();
        if (tmp.status == "Clear")
        {
            RedoClear(tmp.clr_id);
            undo.push(tmp);
        }
        else
        {
            shapeList[tmp.id].isActivated = true;
            undo.push(shapeList[tmp.id]);
        }
        glutPostRedisplay();
        return;
    }
}

//Undo
void Undo()
{
    if (undo.empty())
    {
        cout << "There is nothing can undo.\n";
        return;
    }
    else
    {
        Shape tmp;
        tmp = undo.top();
        undo.pop();
        if (tmp.status == "Clear")
        {
            UndoClear(tmp.clr_id);
            redo.push(tmp);
        }
        else
        {
            shapeList[tmp.id].isActivated = false;
            redo.push(shapeList[tmp.id]);
        }
        glutPostRedisplay();
        return;
    }
}

// Right-click menu
void rightClickMenu(int value)
{
    switch(value)
    {
        case CLEAR:
            ClearAttri();
            break;
        case REDO:
            Redo();
            break;
        case UNDO:
            Undo();
            break;
        case EXIT:
            exit(0);
    }
}

//Filled or not
void filledMenu(int value)
{
    if (value == 1)
        isFilled = true;
    else if (value == 2)
        isFilled = false;
}

//radius menu
void radiusMenu(int r)
{
    if (r == 1.0)
        radius = 1.0;
    else if (r == 2.0)
        radius = 2.0;
    else if (r == 3.0)
        radius = 3.0;
    else if (r == 4.0)
        radius = 4.0;
    else if (r == 5.0)
        radius = 5.0;
}

// line width menu
void lineWidthMenu(int width)
{
    if (width == 1.0)
        line_width = 1.0;
    else if (width == 2.0)
        line_width = 2.0;
    else if (width == 3.0)
        line_width = 3.0;
    else if (width == 4.0)
        line_width = 4.0;
    else if (width == 5.0)
        line_width = 5.0;

    glutPostRedisplay();
}

// Point size menu
void pointSizeMenu(int size)
{
    if (size == 1.0)
        point_size = 1.0;
    else if (size == 2.0)
        point_size = 2.0;
    else if (size == 3.0)
        point_size = 3.0;
    else if (size == 4.0)
        point_size = 4.0;
    else if (size == 5.0)
        point_size = 5.0;
}

// Create right-click menu
void createMenu()
{
    int color_m, type_m, file_m, type_p, type_l, type_r, type_f;
    //create color sub-menu
    color_m = glutCreateMenu(colorMenu);
    glutAddMenuEntry("White", WHITE);
    glutAddMenuEntry("Red", RED);
    glutAddMenuEntry("Green", GREEN);
    glutAddMenuEntry("Blue", BLUE);
    glutAddMenuEntry("Colorful", COLORFUL);

    //create type sub-menu
    type_m = glutCreateMenu(objMenu);
    glutAddMenuEntry("None", NONE);
    glutAddMenuEntry("Polygon", POLYGON);
    glutAddMenuEntry("Circle", CIRCLE);
    glutAddMenuEntry("Point", T_POINT);
    glutAddMenuEntry("Line", LINE);
    glutAddMenuEntry("Triangle", TRIANGLE);
    glutAddMenuEntry("Text", TEXT);
    glutAddMenuEntry("Continuous Line", CONTINUOUS_LINE);
    glutAddMenuEntry("Eraser", ERASER);

    //create point size sub-menu
    type_p = glutCreateMenu(pointSizeMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create line width menu
    type_l = glutCreateMenu(lineWidthMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create radius menu
    type_r = glutCreateMenu(radiusMenu);
    glutAddMenuEntry("1.0", 1.0);
    glutAddMenuEntry("2.0", 2.0);
    glutAddMenuEntry("3.0", 3.0);
    glutAddMenuEntry("4.0", 4.0);
    glutAddMenuEntry("5.0", 5.0);

    //create filled menu
    type_f = glutCreateMenu(filledMenu);
    glutAddMenuEntry("Filled", 1);
    glutAddMenuEntry("NotFilled", 2);

    //create main menu
    glutCreateMenu(rightClickMenu);
    glutAddSubMenu("Colors", color_m);
    glutAddSubMenu("Object", type_m);
    glutAddSubMenu("PointSize", type_p);
    glutAddSubMenu("LineWidth", type_l);
    glutAddSubMenu("Radius", type_r);
    glutAddSubMenu("Filled", type_f);
    glutAddMenuEntry("Redo", REDO);
    glutAddMenuEntry("Undo", UNDO);
    glutAddMenuEntry("Clear", CLEAR);
    glutAddMenuEntry("Exit", EXIT);
    glutAttachMenu(GLUT_RIGHT_BUTTON);
}

// Process Normal Key
void processNormalKeys(unsigned char key, int x, int y)
{
    if (key == 'q' || key == 'Q')
        exit(0);
}

//draw circle
void DrawCircle(struct Shape Cir)
{
    float x, y;
    x = Cir.pnt[0].first;
    y = Cir.pnt[0].second.first;
    float twicePI = 2.0 * PI;
    if (Cir.isFilled)
    {
        int triangleAmount = 50;//要用多少三角形畫出圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glBegin(GL_TRIANGLE_FAN);
        glVertex2f(x, y);
        for (int i = 0; i < triangleAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / triangleAmount)),
                y + (Cir.R * sin(i * twicePI / triangleAmount)));
        }
        glEnd();
    }
    else
    {
        int lineAmount = 150;//要用多少線來畫圓
        
        glColor3f(Cir.pnt[0].second.second.r, Cir.pnt[0].second.second.g, Cir.pnt[0].second.second.b);
        glLineWidth(1.0);
        glBegin(GL_LINE_LOOP);
        for (int i = 0; i < lineAmount; i++)
        {
            glVertex2f(x + (Cir.R * cos(i * twicePI / lineAmount)),
                y + (Cir.R * sin(i * twicePI / lineAmount)));
        }
        glEnd();
    }
    glutPostRedisplay();
}

//draw string
void DrawString(struct Shape Str)
{
    glColor3f(Str.pnt[0].second.second.r, Str.pnt[0].second.second.g, Str.pnt[0].second.second.b);
    glRasterPos2f(Str.pnt[0].first, Str.pnt[0].second.first);
    for (int i = 0; i < Str.str.size(); i++)
    {
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, Str.str[i]);
    }
    glutPostRedisplay();
}

//draw polygon
void DrawPolygon(struct Shape pol)
{
    if (pol.isFilled)
    {
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_POLYGON);
        for (auto iter : pol.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Point
void DrawPoint(struct Shape P)
{
    glPointSize(P.pnt_size);
    glColor3f(P.pnt[0].second.second.r, P.pnt[0].second.second.g, P.pnt[0].second.second.b);
    glBegin(GL_POINTS);
    glVertex2f(P.pnt[0].first, P.pnt[0].second.first);
    glEnd();
    glutPostRedisplay();
}

//Draw Triangle
void DrawTriangle(struct Shape Tri)
{
    if (Tri.isFilled)
    {
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
    }
    else
    {
        glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);
        glBegin(GL_TRIANGLES);
        for (auto iter : Tri.pnt)
        {
            glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
            glVertex2f(iter.first, iter.second.first);
        }
        glEnd();
        glPolygonMode(GL_FRONT_AND_BACK, GL_FILL);
    }
    glutPostRedisplay();
}

//Draw Line
void DrawLine(struct Shape L)
{
    glLineWidth(L.lineWidth);
    glColor3f(L.pnt[0].second.second.r, L.pnt[0].second.second.g, L.pnt[0].second.second.b);
    glBegin(GL_LINES);
    glVertex2f(L.pnt[0].first, L.pnt[0].second.first);
    glVertex2f(L.pnt[1].first, L.pnt[1].second.first);
    glEnd();
    glutPostRedisplay();
}

//Draw Continuous line
void DrawContLine(struct Shape CL)
{
    glLineWidth(CL.lineWidth);
    //glColor3f(CL.pnt[0].second.second.r, CL.pnt[0].second.second.g, CL.pnt[0].second.second.b);
    glBegin(GL_LINE_STRIP);
    for (auto iter : CL.pnt)
    {
        glColor3f(iter.second.second.r, iter.second.second.g, iter.second.second.b);
        glVertex2f(iter.first, iter.second.first);
    }
    glEnd();
    glutPostRedisplay();
}

// draw main func
void Draw()
{
    for (int i = 0; i < shapeCount; i++)
    {//NONE, POLYGON, CIRCLE, T_POINT, LINE, TRIANGLE, TEXT
        if (shapeList[i].isActivated)
        {
            if (shapeList[i].type == "Polygon")
            {
                DrawPolygon(shapeList[i]);
            }
            if (shapeList[i].type == "Circle")
            {
                DrawCircle(shapeList[i]);
            }
            if (shapeList[i].type == "Point")
            {
                DrawPoint(shapeList[i]);
            }
            if (shapeList[i].type == "Triangle")
            {
                DrawTriangle(shapeList[i]);
            }
            if (shapeList[i].type == "Line")
            {
                DrawLine(shapeList[i]);
            }
            if (shapeList[i].type == "Text")
            {
                DrawString(shapeList[i]);
            }
            if (shapeList[i].type == "ContinuousLine")
            {
                DrawContLine(shapeList[i]);
            }
            if (shapeList[i].type == "Eraser")
            {
                DrawContLine(shapeList[i]);
            }
        }
    }
    //glutPostRedisplay();
}

// Initialize
void init()
{
    glClearColor(0.0, 0.0, 0.0, 0.0);
    glColor3f(1.0f, 1.0f, 1.0f);
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, (double) width, 0, (double) height);
}

void display_func(void)
{
    glClearColor(0.0, 0.0, 0.0, 0.0); //set window background color
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); // clear color buffer
    glViewport(0, 0, width, height);
    Draw();
}

//reshape func
void changeSize(int w, int h)
{
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    width = w; height = h;
    glOrtho(0.0, w, 0.0, h, -1.0, 1.0);
    glViewport(0, 0, w, h);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    glutPostRedisplay();
}

// Mouse Event
void mouse_func(int button, int state, int x, int y)
{
    if (button != GLUT_LEFT_BUTTON)/* || state != GLUT_DOWN*/
        return;

    if (obj_type == CONTINUOUS_LINE || obj_type == ERASER)
    {
//        cout << "Not ready\n";
        if (state == GLUT_DOWN)
        {
            float wx, wy;
            wx = (float)x;
            wy = (float)(height - y);

            if (shapeList[shapeCount].pnt.empty())
            {
                StartDrawing(wx, wy);
            }
        }
        else
        {
            float wx, wy;
            wx = (float)x;
            wy = (float)(height - y);
            dragging = false;
            shapeList[shapeCount].limit = 0;
            FinishDrawing(wx, wy);
        }
    }
    else
    {
        if (state != GLUT_DOWN)
            return;
        float wx, wy;
        wx = (float)x;
        wy = (float)(height - y);

        if (shapeList[shapeCount].pnt.empty())
        {
            StartDrawing(wx, wy);
        }
        else if (shapeList[shapeCount].pnt.back().first == wx && shapeList[shapeCount].pnt.back().second.first == wy)
        {
            shapeList[shapeCount].limit = 0;
        }
        else
        {
            ContDrawing(wx, wy);
        }
        FinishDrawing(wx, wy);
    }
}

// motion func
void motion(int x, int y)
{
    if (!dragging)
        return;
    float wx, wy;
    wx = (float)x;
    wy = (float)(height - y);
    cout << wx << "," << wy << endl;
        //TODO
    ContDrawing(wx, wy);
}

int main(int argc, char **argv)
{
    glutInit(&argc, argv);
    glutInitWindowPosition(0, 0);
    glutInitWindowSize(width, height);
    glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
    glutCreateWindow("CGhw_2DPainter");
    createMenu();
    init();
    // TODO:
    
    glutDisplayFunc(display_func);// register callbacks
    
    glutReshapeFunc(changeSize);  
    glutKeyboardFunc(processNormalKeys);
    glutMouseFunc(mouse_func);
    glutMotionFunc(motion);
    
    glutMainLoop();
    return 1;
}
```