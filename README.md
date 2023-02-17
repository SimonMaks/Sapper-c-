# Sapper-c-
A code of a "Sapper" game created by an AI


#include <iostream>
#include <vector>
#include <ctime>
#include <cstdlib>

using namespace std;

const int FIELD_WIDTH = 10;
const int FIELD_HEIGHT = 10;
const int BOMB_COUNT = 10;
const char COVERED = '-';
const char FLAGGED = 'F';
const char BOMB = '*';

class Field {
public:
    Field(int width, int height, int bombCount);
    void show(bool showAll = false) const;
    bool uncover(int x, int y);
    bool toggleFlag(int x, int y);
    bool isSolved() const;
    bool isGameOver() const;
private:
    int width, height, bombCount;
    vector<vector<char>> field, bombMap, uncovered;
    void placeBombs();
    void countBombs();
};

Field::Field(int width, int height, int bombCount) : width(width), height(height), bombCount(bombCount) {
    field = vector<vector<char>>(height, vector<char>(width, COVERED));
    bombMap = vector<vector<char>>(height, vector<char>(width, ' '));
    uncovered = vector<vector<char>>(height, vector<char>(width, ' '));
    placeBombs();
    countBombs();
}

void Field::placeBombs() {
    srand(time(NULL));
    int bombsPlaced = 0;
    while (bombsPlaced < bombCount) {
        int x = rand() % width;
        int y = rand() % height;
        if (bombMap[y][x] != BOMB) {
            bombMap[y][x] = BOMB;
            bombsPlaced++;
        }
    }
}

void Field::countBombs() {
    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            if (bombMap[y][x] == BOMB) {
                continue;
            }
            int count = 0;
            for (int dy = -1; dy <= 1; dy++) {
                for (int dx = -1; dx <= 1; dx++) {
                    if (x + dx >= 0 && x + dx < width && y + dy >= 0 && y + dy < height) {
                        if (bombMap[y + dy][x + dx] == BOMB) {
                            count++;
                        }
                    }
                }
            }
            if (count > 0) {
                bombMap[y][x] = '0' + count;
            }
        }
    }
}

void Field::show(bool showAll) const {
    cout << "  ";
    for (int x = 0; x < width; x++) {
        cout << x << ' ';
    }
    cout << endl;
    for (int y = 0; y < height; y++) {
        cout << y << ' ';
        for (int x = 0; x < width; x++) {
            if (showAll || uncovered[y][x] == ' ') {
                cout << field[y][x] << ' ';
            } else {
                cout << uncovered[y][x] << ' ';
            }
        }
        cout << endl;
    }
}

bool Field::uncover(int x, int y) {
    if (field[y][x] == FLAGGED || uncovered[y][x] != ' ') {
        return false;
    }
    if (bombMap[y][x] == BOMB) {
        field[y][x] = BOMB;
        return true;
    }
    if (bombMap[y][x] != ' ')
        {
    uncovered[y][x] = bombMap[y][x];
}
else
{
    uncovered[y][x] = '0';
    for (int dy = -1; dy <= 1; dy++) {
        for (int dx = -1; dx <= 1; dx++) {
            if (x + dx >= 0 && x + dx < width && y + dy >= 0 && y + dy < height) {
                if (dy != 0 || dx != 0) {
                    uncover(x + dx, y + dy);
                }
            }
        }
    }
}
return false;

}

bool Field::toggleFlag(int x, int y) {
    if (uncovered[y][x] != ' ') {
    return false;
    }
if (field[y][x] == COVERED) {
    field[y][x] = FLAGGED;
    } else if (field[y][x] == FLAGGED) {
    field[y][x] = COVERED;
    }
    return true;
}

bool Field::isSolved() const {
    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            if (field[y][x] == COVERED && bombMap[y][x] != BOMB) {
            return false;
            }
        }
    }
    return true;
}

bool Field::isGameOver() const {
    for (int y = 0; y < height; y++) {
        for (int x = 0; x < width; x++) {
            if (field[y][x] == BOMB) {
            return true;
            }
        }
    }
    return false;
}

int main() {
    Field field(FIELD_WIDTH, FIELD_HEIGHT, BOMB_COUNT);
    while (!field.isGameOver() && !field.isSolved()) {
        field.show();
        int x, y;
        char action;
        cout << "Enter x, y, and action (u = uncover, f = flag/unflag): ";
        cin >> x >> y >> action;
        if (action == 'u') {
            if (field.uncover(x, y)) {
            cout << "You hit a bomb! Game over." << endl;
            }
        } else if (action == 'f') {
        field.toggleFlag(x, y);
        }
    }
    if (field.isSolved()) {
    cout << "Congratulations, you win!" << endl;
    }
    field.show(true);
    return 0;
}
